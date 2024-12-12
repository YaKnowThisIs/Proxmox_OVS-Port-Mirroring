# 2 - Configuration du Port Mirroring

## Étape 1 : Créer le script hook
Ce script va se charger :
- D'activer le port mirroring sur la VM lors du démarrage de cette dernière
- De désactiver le port mirroring sur la VM lors de l'arrêt de cette dernière
  
```
nano /var/lib/vz/snippets/config_mirroring_.sh
```
- Contenu du script (pour vmbr03)
```
#!/usr/bin/env bash

# ID de la VM et phase d'exécution passés automatiquement par Proxmox
VM_ID=$1
PHASE=$2

# Paramètres pour le bridge OVS et le nom du miroir
BRIDGE="vmbr03"
MIRROR_NAME="MIRROR_${VM_ID}"
PORT_NAME="tap${VM_ID}i1"

function create_mirror {
    echo "Création du miroir pour le port ${PORT_NAME} sur le bridge ${BRIDGE} pour la VM ${VM_ID}..."

    # Boucle pour vérifier que le port TAP est actif
    for i in {1..10}; do
        if ovs-vsctl list Interface "${PORT_NAME}" > /dev/null 2>&1; then
            echo "Port ${PORT_NAME} détecté, création du miroir en cours..."
            # Créer le miroir sur le port détecté
            ovs-vsctl -- --id=@${PORT_NAME} get Port "${PORT_NAME}" \
                       -- --id=@m create Mirror name="${MIRROR_NAME}" select-all=true output-port=@${PORT_NAME} \
                       -- set Bridge "${BRIDGE}" mirrors=@m
            echo "Miroir ${MIRROR_NAME} créé avec succès pour la VM ${VM_ID}."
            break
        else
            echo "Port ${PORT_NAME} non encore disponible, nouvelle vérification dans 1 seconde..."
            sleep 1
        fi
    done

    # Vérification si le miroir a été créé après 10 tentatives
    if ! ovs-vsctl list Mirror | grep -q "${MIRROR_NAME}"; then
        echo "Échec de la création du miroir : port ${PORT_NAME} introuvable pour la VM ${VM_ID}."
    fi
}

function clear_mirror {
    echo "Suppression du miroir ${MIRROR_NAME} sur le bridge ${BRIDGE} pour la VM ${VM_ID}..."
    ovs-vsctl -- --id=@m get Mirror "${MIRROR_NAME}" -- remove Bridge "${BRIDGE}" mirrors @m
    echo "Miroir ${MIRROR_NAME} supprimé pour la VM ${VM_ID}."
}

# Exécution des actions selon la phase de démarrage/arrêt
if [[ "$PHASE" == "post-start" ]]; then
    # Nettoyer les miroirs précédents avant de créer un nouveau
    clear_mirror
    create_mirror

elif [[ "$PHASE" == "pre-stop" ]]; then
    # Supprimer le miroir avant l'arrêt de la VM
    clear_mirror
fi
```
- Sauvegarder et quitter

## Étape 2 : Rendre le script exécutable
```
chown root:root
chmod 750 /var/lib/vz/snippets/config_mirroring_.sh
```

## Étape 3 : Affecter le script à la VM (ici il s’agit de l’ID 2002)
```
qm set 2002 --hookscript local:snippets/config_mirroring_.sh
```

## Étape 4 : Vérifier le Miroir (sur Proxmox)
```
ovs-vsctl list Mirror
```

## Étape 5 : Configurer les paramètres réseau de la VM (sur la VM)
- Configurer les paramètres de la carte réseau
```
nano /etc/network/interfaces
```
Exemple de configuration de l’interface :
```
allow-hotplug ens19
auto ens19
iface ens19 inet manual
	up ip link set ens19 promisc on
	down ip link set ens19 promisc off
```
- Redémarrer le service réseau pour appliquer les modifications
```
systemctl restart networking
```

## Étape 6 : Écouter le Trafic (sur la VM de surveillance)
Utiliser tcpdump pour écouter le trafic sur l’interface ens19 (liée via OVS à tap2002i1)
```
tcpdump -i ens19
```
Générer du trafic depuis une VM située dans le réseau de l'interface TAP de la VM de surveillance
