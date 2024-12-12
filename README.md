# Port TAP (mirroring) sur Proxmox avec Open vSwitch

# Port Mirroring avec Open vSwitch sur Proxmox

Bienvenue dans ce dépôt GitHub dédié à la mise en place d'un port mirroring avec Open vSwitch sur une plateforme Proxmox. Ce guide couvre l'installation et la configuration nécessaires pour configurer le port mirroring afin d'analyser le trafic réseau.

## 🚀 Contenu du Dépôt

### 1 - 🛠️ Préparatifs : Installation et Configuration d'Open vSwitch
Guide étape par étape pour installer Open vSwitch sur Proxmox et configurer un switch virtuel.

📜 Lien vers la procédure : [Installation et Configuration d'Open vSwitch](#)

### 2 - ⚙️ Configuration du Port Mirroring
Configuration détaillée pour activer le port mirroring
- Création du port TAP pour la VM
- Automatisation de la création du mirroir (script)
- Configuration de l'interface réseau de la VM
- Validation et tests

📜 Lien vers la procédure : [Configuration du Port Mirroring](#)

## 📖 Prérequis
Avant de commencer, assurez-vous d'avoir les éléments suivants :
- Un serveur Proxmox
- Accès root ou privilèges sudo pour la configuration
- Une machine virtuelle de surveillance (pour utiliser le mirroring)
- Une machine virtuelle surveillée (pour générer du trafic)
- Connaissance de base des commandes Linux, de l'administration réseau, et de Proxmox

## 📝 Contribution
Ce projet est ouvert aux contributions. Si vous souhaitez ajouter des fonctionnalités, corriger des bugs, ou améliorer la documentation, vous pouvez suivre ces étapes :

1. Forkez le projet
2. Créez une branche pour votre fonctionnalité ou correction
3. Commitez vos modifications
4. Poussez votre branche
5. Créez une Pull Request

## 📧 Support
Pour toute question, suggestion ou assistance, n'hésitez pas à me contacter directement : [yanis.afendou@nisya-consulting.com](mailto:yanis.afendou@nisya-consulting.com)

## 🔒 Licence
Ce projet est sous licence GNU General Public License v3.0 - voir le fichier [LICENSE](#) pour plus de détails.
