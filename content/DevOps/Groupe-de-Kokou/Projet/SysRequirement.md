---
title: Configuration Système Requise
---


## Système d'exploitation

**Linux uniquement** - Ce projet nécessite un système Linux.

### Distributions testées et supportées

| Distribution | Version minimale | Statut |
|--------------|------------------|--------|
| Ubuntu | 20.04 LTS | Testé |
| Debian | 11 | Testé |
| Linux Mint | 20+ | Compatible |
| Pop!_OS | 20.04+ | Compatible |

### Non supporté

- Windows (même avec WSL2)
- macOS
- Autres systèmes d'exploitation

---

## Dépendances

Les scripts d'installation automatique gèrent les dépendances suivantes :

### Pour Minikube (Développement Local)

Les dépendances suivantes seront installées automatiquement si manquantes :

| Outil | Version | Description |
|-------|---------|-------------|
| Docker | Latest | Conteneurisation |
| kubectl | Latest | Client Kubernetes |
| Minikube | Latest | Cluster Kubernetes local |
| Conntrack | Latest | Suivi des connexions |
| Socat | Latest | Relais de données |

### Pour EKS (Production)

Les dépendances suivantes seront installées automatiquement si manquantes :

| Outil | Version | Description |
|-------|---------|-------------|
| AWS CLI | v2 | Interface en ligne de commande AWS |
| Terraform | Latest | Infrastructure as Code |
| kubectl | Latest | Client Kubernetes |
| jq | Latest | Traitement JSON |
| Docker | - | Doit être pré-installé |

---

## Stratégie d'images Docker

### Pas de Docker Hub

Ce projet **n'utilise PAS Docker Hub**. Les images sont gérées localement :

#### Minikube (Développement)

- Les images sont **buildées localement** dans le daemon Docker de Minikube
- `imagePullPolicy: Never` dans les manifests Kubernetes
- Commande utilisée : `eval $(minikube docker-env)` puis `docker build`

#### EKS (Production)

- Les images sont **buildées localement** puis **poussées vers Amazon ECR**
- Pas de pull depuis Docker Hub
- Amazon ECR sert de registry privé

---

## Configuration matérielle

### Minimum pour Minikube

| Ressource | Minimum | Recommandé |
|-----------|---------|------------|
| RAM | 4 GB | 8 GB |
| CPUs | 2 | 4 |
| Disque | 20 GB libre | 40 GB libre |
| Connexion | Internet requis | Haute vitesse |

### Minimum pour EKS

| Ressource | Description |
|-----------|-------------|
| Connexion Internet | Requise pour AWS API |
| Disque | 10 GB libre (pour images Docker) |
| Credentials AWS | Access Key + Secret Key |

---

## Permissions requises

### Minikube

- **sudo** : Requis pour l'installation des dépendances et le démarrage de Minikube
- Raison : Docker et Minikube nécessitent des privilèges root

### EKS

- **Pas de sudo** : Les scripts EKS ne nécessitent pas de privilèges root
- **Permissions AWS** : Votre compte AWS doit avoir les permissions pour :
  - Créer des clusters EKS
  - Créer des repositories ECR
  - Créer des VPCs et subnets
  - Créer des IAM roles
  - Créer des LoadBalancers

---

## Connectivité réseau

### Ports utilisés (Minikube)

| Port | Service | Protocole |
|------|---------|-----------|
| 30500 | Backend API | HTTP |
| 30080 | Frontend | HTTP |
| Dynamique | Minikube API | HTTPS |

### Ports utilisés (EKS)

| Port | Service | Protocole |
|------|---------|-----------|
| 80 | Frontend LoadBalancer | HTTP |
| 443 | EKS API Server | HTTPS |
| 5000 | Backend (interne) | HTTP |
| 8080 | Frontend (interne) | HTTP |

### Accès Internet requis

Les deux environnements nécessitent une connexion Internet pour :

- Télécharger les images de base Docker
- Installer les dépendances système
- Accéder aux APIs (AWS pour EKS)
- Télécharger les binaires (kubectl, Minikube, Terraform, etc.)

---

## Vérification de la configuration

### Vérifier le système d'exploitation

```bash
# Doit afficher "Linux"
uname -s

# Vérifier la distribution
lsb_release -a
```

### Vérifier les ressources disponibles

```bash
# Vérifier la RAM disponible
free -h

# Vérifier les CPUs
nproc

# Vérifier l'espace disque
df -h
```

### Vérifier Docker (si pré-installé)

```bash
# Version Docker
docker --version

# Statut du service
sudo systemctl status docker

# Tester Docker
sudo docker run hello-world
```

### Vérifier les credentials AWS (pour EKS)

```bash
# Afficher les credentials configurés
env | grep AWS

# Tester la connexion AWS
aws sts get-caller-identity
```

---

## Installation préalable recommandée

Bien que les scripts installent automatiquement les dépendances, vous pouvez pré-installer Docker :

### Installation de Docker (Ubuntu/Debian)

```bash
# Mettre à jour les paquets
sudo apt update

# Installer Docker
sudo apt install -y docker.io

# Démarrer Docker
sudo systemctl start docker
sudo systemctl enable docker

# Ajouter votre utilisateur au groupe docker
sudo usermod -aG docker $USER

# Se reconnecter pour appliquer les changements
```

### Vérifier l'installation

```bash
docker --version
docker ps
```

---

## Coûts et considérations

### Minikube (Gratuit)

- Aucun coût
- Utilise uniquement les ressources locales
- Idéal pour le développement et les tests

### EKS (Payant)

**Estimation mensuelle** : ~190 EUR/mois

| Composant | Coût mensuel (EUR) |
|-----------|-------------------|
| EKS Control Plane | ~73 EUR |
| EC2 t3.medium (2 instances) | ~60 EUR |
| NAT Gateway | ~40 EUR |
| Elastic Load Balancer | ~15 EUR |
| Transfert de données | ~5 EUR |
| **Total** | **~190 EUR** |

**IMPORTANT** : N'oubliez pas de détruire l'infrastructure après les tests pour éviter les frais :

```bash
./deploy-eks-oneclick.sh --destroy
```

---

## Troubleshooting système

### Problème : Pas assez de RAM pour Minikube

**Solution** :
```bash
# Réduire la RAM allouée
sudo ./deploy-minikube-oneclick.sh --memory 2048
```

### Problème : Docker daemon non démarré

**Solution** :
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### Problème : Permissions insuffisantes

**Solution** :
```bash
# Pour Minikube : Utiliser sudo
sudo ./deploy-minikube-oneclick.sh

# Pour Docker : Ajouter l'utilisateur au groupe
sudo usermod -aG docker $USER
# Puis se reconnecter
```

### Problème : Pas d'accès Internet

**Solution** :
- Vérifier la connexion réseau
- Vérifier les paramètres proxy si applicable
- Tester : `ping google.com`

---

## Sécurité

### Credentials AWS

- **Ne jamais commiter** les credentials dans Git
- Utiliser des variables d'environnement
- Configurer des secrets GitHub pour CI/CD
- Rotation régulière des Access Keys

### Exemple de configuration sécurisée

```bash
# Dans ~/.bashrc ou ~/.zshrc (ne pas commiter ce fichier)
export AWS_ACCESS_KEY_ID="votre_key"
export AWS_SECRET_ACCESS_KEY="votre_secret"

# Puis sourcer le fichier
source ~/.bashrc
```

---

## Résumé des commandes de vérification

```bash
# Système
uname -s                  # Doit afficher "Linux"
lsb_release -a           # Distribution

# Ressources
free -h                  # RAM
nproc                    # CPUs
df -h                    # Disque

# Docker
docker --version         # Version
sudo docker ps          # Statut

# AWS (pour EKS)
aws --version           # AWS CLI
aws sts get-caller-identity  # Credentials
```

---

## Support et documentation

- Pour les problèmes d'installation : Consulter les logs des scripts (mode `--verbose`)
- Pour les problèmes de permissions : Vérifier `sudo` pour Minikube
- Pour les problèmes AWS : Vérifier les credentials et permissions IAM
- Pour les problèmes de ressources : Ajuster les paramètres `--memory` et `--cpus`

---

**Version** : 1.0.0  
**Dernière mise à jour** : Décembre 2025