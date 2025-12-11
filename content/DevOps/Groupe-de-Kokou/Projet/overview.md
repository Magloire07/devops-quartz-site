---
title: 1-Project overview
---


# PROJET DEVOPS

**Repos**

[](https://github.com/Secr3ts/msg_devops.git)

**Cahier des charges**

[final-project-devops-for-swe-2025.pdf](images/final-project-devops-for-swe-2025.pdf)

<aside>


# Résumé du service

</aside>



### Partie Front: (OUNISSA)

Framework: [https://vite.dev/guide/](https://vite.dev/guide/)

### Partie Back: (AISSATOU & ALOIS)

### Partie  déploiement: (KOKOU)

## Répartition des TDs

- Aissatou : td3
- Ounissa : td4
- Kokou : td5
- Aloïs : td6

<aside>

# MSG DevOps - Déploiement Kubernetes

## Vue d'ensemble
Mini jeu de quiz , avec scoreboard intégré qui se reset tout les 24h.

Chaque participant qui complète le jeu peut ajouter son temps au scoreboard en ajoutant son username.

**Stack technique** :
- Backend : Python (Flask) avec SQLite
- Frontend : Vue.js
- Infrastructure : Kubernetes (Minikube ou EKS)
- CI/CD : GitHub Actions
- IaC : Terraform

---

## Démarrage rapide

### Mode développement local (sans Kubernetes)

#### Backend

```bash
cd backend

# Installer les dépendances
pip install -r requirements.txt

# Lancer le serveur Flask
python app.py
```

Le backend sera accessible sur `http://localhost:5000`

#### Frontend

```bash
cd frontend

# Installer les dépendances
npm install

# Lancer le serveur de développement
npm run dev
```

Le frontend sera accessible sur `http://localhost:3000`

**Note** : Le fichier `.env.development` configure automatiquement l'API URL vers `http://localhost:5000`

---

### Minikube (Local)

```bash
sudo ./deploy-minikube-oneclick.sh
```

Le script gère automatiquement :
- Installation de Docker, kubectl, Minikube
- Build des images localement
- Déploiement complet
- Affichage des URLs

### EKS (Production)

```bash
# Configurer AWS
export AWS_ACCESS_KEY_ID="votre_key"
export AWS_SECRET_ACCESS_KEY="votre_secret"

# Déployer
./deploy-eks-oneclick.sh
```

Le script gère automatiquement :
- Installation des outils AWS (CLI, Terraform, kubectl)
- Provisionnement de l'infrastructure
- Build et push vers ECR
- Déploiement complet
- Affichage de l'URL publique

---

## Prérequis

- **Système d'exploitation** : Linux uniquement (Ubuntu/Debian recommandé)
- **Connexion Internet** : Requise
- **Docker** : Installé automatiquement pour Minikube, requis pour EKS
- **Credentials AWS** : Uniquement pour EKS

---

## Structure du projet

```
msg_devops/
├── backend/                    # API Python Flask
│   ├── app.py
│   ├── question_dao.py
│   ├── Dockerfile
│   └── requirements.txt
├── frontend/                   # Application Vue.js
│   ├── src/
│   ├── Dockerfile
│   └── package.json
├── infra/                      # Infrastructure Terraform
│   ├── main.tf
│   ├── eks/
│   └── outputs.tf
├── k8s/                        # Manifests Kubernetes (EKS)
│   ├── backend-deployment.yaml
│   ├── backend-service.yaml
│   ├── frontend-deployment.yaml
│   └── frontend-service.yaml
├── k8s-minikube/              # Manifests Kubernetes (Minikube)
│   └── (mêmes fichiers adaptés pour local)
├── .github/workflows/         # CI/CD GitHub Actions
│   └── ci-cd.yaml
├── deploy-minikube-oneclick.sh
├── deploy-eks-oneclick.sh
└── documentation...
```

---

## Documentation complète

| Document | Description |
|----------|-------------|
| **ONECLICK_GUIDE.md** | Guide complet des scripts de déploiement |
| **DEPLOYMENT_MINIKUBE.md** | Guide détaillé Minikube |
| **DEPLOYMENT_EKS.md** | Guide détaillé EKS |
| **DEPLOYMENT_COMPARISON.md** | Comparaison Minikube vs EKS |
| **SYSTEM_REQUIREMENTS.md** | Prérequis système détaillés |
| **SQLITE_NOTES.md** | Notes sur SQLite et migration |

---

## Fonctionnalités

### Application

- Gestion de questions de quiz
- Interface d'administration
- API RESTful
- Authentification JWT
- Upload d'images

### Infrastructure

- Déploiement automatisé ONE-CLICK
- Build et push d'images automatiques
- Configuration Kubernetes complète
- Persistence des données (PVC)
- CI/CD avec GitHub Actions (EKS)

---

## Limitations actuelles

| Limitation | Raison | Solution |
|------------|--------|----------|
| Backend limité à 1 replica | SQLite ne supporte pas les accès concurrents | Migrer vers PostgreSQL/RDS |
| Pas de HTTPS sur Minikube | Environnement local | Normal pour dev |

Consultez **SQLITE_NOTES.md** pour les détails sur la migration vers PostgreSQL.

---

## CI/CD

Le workflow GitHub Actions est configuré pour déployer automatiquement sur EKS à chaque push sur `main`.

**Configuration** :
1. Ajouter les secrets GitHub :
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
2. Pousser sur `main` : Le déploiement se fait automatiquement

**Workflow** : `.github/workflows/ci-cd.yaml`

---

## Commandes utiles

### Minikube

```bash
# Déployer
sudo ./deploy-minikube-oneclick.sh

# Mettre à jour après modification du code
sudo ./deploy-minikube-oneclick.sh --update backend
sudo ./deploy-minikube-oneclick.sh --update frontend

# Voir les pods
kubectl get pods

# Logs
kubectl logs <pod-name>

# URL du frontend
minikube service frontend --url

# Désinstaller
sudo ./deploy-minikube-oneclick.sh --uninstall
```

### EKS

```bash
# Vérifier les prérequis
./deploy-eks-oneclick.sh --check-only

# Déployer
./deploy-eks-oneclick.sh

# Voir les pods
kubectl get pods

# URL du LoadBalancer
kubectl get service frontend

# Détruire l'infrastructure
./deploy-eks-oneclick.sh --destroy
```

---

## Support

En cas de problème :

1. Consulter la documentation dans les fichiers `.md`
2. Utiliser le mode verbose : `--verbose`
3. Vérifier les logs : `kubectl logs <pod-name>`
4. Pour EKS : Vérifier les credentials AWS

---

