# 1 - Préparatifs : Installation et Configuration d'Open vSwitch

> !!! Les exemples sont proposés avec vmbr03 et la VM ID 2002 (à adapter selon votre configuration) !!!
> Cette procédure a été testée et validée sur Proxmox 8.2.7

## Étape 1 : Installer Open vSwitch
```
apt update
apt install openvswitch-switch
```

## Étape 2 : Créer un Nouveau Bridge OVS (vmbr03)
- Créer le bridge OVS vmbr03 (depuis l’interface graphique)
- Vérifiez que le bridge OVS a été créé (vmbr03 doit apparaître dans la liste)
```
ovs-vsctl list-br
```

## Étape 3 : Configurer le Bridge dans Proxmox
Vérifier la configuration réseau de Proxmox :
- Vérifier le bridge dans le fichier de configuration réseau
```
less /etc/network/interfaces
```
- Exemple de configuration minimale :
```
auto vmbr03
iface vmbr03 inet manual
    ovs_type OVSBridge
```
- Redémarrer le service réseau pour appliquer les modifications
```
systemctl restart networking
```

## Étape 4 : Créer un Port TAP pour la VM
Avant de démarrer votre VM, ajoutez à cette dernière (via l’interface graphique) l’interface réseau (sélectionner le Bridge vmbr03).
Décocher firewall.
Cette interface doit générer le port TAP qui sera utilisé pour le mirroring.

## Étape 5 : Vérification de la VM pour l’utiliser le Bridge
- Ouvrez le fichier de configuration de la VM
```
nano /etc/pve/qemu-server/2002.conf
```
Vérifier la ligne pour correspondant au bridge vmbr03
Exemple :
```
net0: virtio=XX:XX:XX:XX:XX:XX,bridge=vmbr03
```
## Étape 6 : Démarrer la VM
```
qm start 2002
```
