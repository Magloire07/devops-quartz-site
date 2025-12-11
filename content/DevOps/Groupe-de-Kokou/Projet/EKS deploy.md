---
title: EKS deploy
---

# Guide de Déploiement EKS (Production)

## Vue d'ensemble

Ce guide décrit le déploiement de l'application msg-devops sur un cluster AWS EKS (Elastic Kubernetes Service) en production.

**Environnement** : Production AWS  
**Coût estimé** : ~190 EUR/mois  
**Durée d'installation** : ~20-30 minutes

---

## Utilisation du script ONE-CLICK

### Déploiement rapide

```bash
# Configurer les credentials AWS
export AWS_ACCESS_KEY_ID="votre_key"
export AWS_SECRET_ACCESS_KEY="votre_secret"

# Déployer
./deploy-eks-oneclick.sh
```

Le script gère automatiquement :
- Vérification des credentials AWS
- Installation des outils (AWS CLI, Terraform, kubectl)
- Provisionnement de l'infrastructure (EKS, ECR, VPC, IAM)
- Build et push des images vers ECR
- Déploiement de l'application sur EKS
- Affichage de l'URL du LoadBalancer

### Options avancées

```bash
# Vérifier les prérequis uniquement
./deploy-eks-oneclick.sh --check-only

# Déploiement automatique (sans confirmation Terraform)
./deploy-eks-oneclick.sh --auto-approve

# Skip Terraform si infrastructure existe
./deploy-eks-oneclick.sh --skip-terraform

# Changer de région
./deploy-eks-oneclick.sh --region us-east-1

# Détruire l'infrastructure
./deploy-eks-oneclick.sh --destroy
```

Pour plus de détails, consultez **ONECLICK_GUIDE.md**.

---

## Architecture AWS

### Infrastructure provisionnée

| Ressource | Type | Description |
|-----------|------|-------------|
| EKS Cluster | Kubernetes 1.28 | Cluster managé |
| ECR | Repositories | 2 repos (backend/frontend) |
| VPC | Network | VPC dédiée avec subnets |
| NAT Gateway | Network | Accès Internet pour private subnets |
| IAM Roles | Security | Rôles pour EKS et nodes |
| Security Groups | Security | Contrôle du trafic |
| Load Balancer | Network | ALB pour frontend |

### Composants Kubernetes déployés

| Composant | Type | Replicas | Port |
|-----------|------|----------|------|
| Backend | Deployment | 1 | 5000 |
| Frontend | Deployment | 2 | 8080 |
| SQLite PVC | PersistentVolume | - | 1Gi |
| Backend Service | ClusterIP | - | 5000 |
| Frontend Service | LoadBalancer | - | 80 |

### Images Docker

| Image | Registre | Tag |
|-------|----------|-----|
| msg-devops-backend | ECR | latest |
| msg-devops-frontend | ECR | latest |

**Note** : Les images sont buildées localement puis poussées vers Amazon ECR (pas de Docker Hub).

---

## Prérequis

### Système d'exploitation

- **Linux uniquement** (Ubuntu/Debian recommandé)
- Windows et macOS non supportés

### Compte AWS

- Compte AWS actif
- Access Key ID
- Secret Access Key
- Permissions IAM requises (voir section Permissions)

### Logiciels

Les outils suivants seront installés automatiquement par le script si manquants :
- AWS CLI v2
- Terraform
- kubectl
- jq

**Docker doit être pré-installé** pour builder les images.

---

## Configuration AWS

### 1. Obtenir les credentials AWS

#### Via la console AWS

1. Connectez-vous à la console AWS : https://console.aws.amazon.com
2. Naviguez vers **IAM** → **Users**
3. Sélectionnez votre utilisateur
4. Onglet **Security credentials**
5. Cliquez sur **Create access key**
6. Choisissez **Command Line Interface (CLI)**
7. Copiez l'Access Key ID et le Secret Access Key

#### Configurer les credentials

```bash
export AWS_ACCESS_KEY_ID="AKIAIOSFODNN7EXAMPLE"
export AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
export AWS_REGION="eu-west-3"  # Optionnel, défaut: eu-west-3
```

### 2. Vérifier la connexion

```bash
aws sts get-caller-identity
```

Résultat attendu :
```json
{
    "UserId": "AIDAI...",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/your-username"
}
```

### 3. Permissions IAM requises

Votre utilisateur AWS doit avoir les permissions suivantes :

| Service | Permissions |
|---------|-------------|
| EKS | `AmazonEKSClusterPolicy` |
| EC2 | `AmazonEC2FullAccess` |
| VPC | `AmazonVPCFullAccess` |
| IAM | `IAMFullAccess` (ou créer des rôles manuellement) |
| ECR | `AmazonEC2ContainerRegistryFullAccess` |
| ELB | `ElasticLoadBalancingFullAccess` |

**Note** : Pour la production, utilisez des permissions plus restrictives.

---

## Déploiement manuel (sans script)

Si vous préférez déployer manuellement, suivez ces étapes :

### 1. Installer les outils

#### AWS CLI v2

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

#### Terraform

```bash
curl -LO https://releases.hashicorp.com/terraform/1.6.0/terraform_1.6.0_linux_amd64.zip
unzip terraform_1.6.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/
```

#### kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### 2. Provisionner l'infrastructure

```bash
cd infra
terraform init
terraform plan
terraform apply
```

Ressources créées :
- Cluster EKS
- 2 repositories ECR
- VPC avec subnets publics/privés
- NAT Gateway
- IAM roles et policies

### 3. Configurer kubectl

```bash
aws eks update-kubeconfig --region eu-west-3 --name msg-devops-cluster
kubectl get nodes
```

### 4. Builder et pousser les images

```bash
# Récupérer l'Account ID
export AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)

# Login à ECR
aws ecr get-login-password --region eu-west-3 | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.eu-west-3.amazonaws.com

# Builder et pousser le backend
cd backend
docker build -t msg-devops-backend:latest .
docker tag msg-devops-backend:latest $AWS_ACCOUNT_ID.dkr.ecr.eu-west-3.amazonaws.com/msg-devops-backend:latest
docker push $AWS_ACCOUNT_ID.dkr.ecr.eu-west-3.amazonaws.com/msg-devops-backend:latest

# Builder et pousser le frontend
cd ../frontend
docker build -t msg-devops-frontend:latest .
docker tag msg-devops-frontend:latest $AWS_ACCOUNT_ID.dkr.ecr.eu-west-3.amazonaws.com/msg-devops-frontend:latest
docker push $AWS_ACCOUNT_ID.dkr.ecr.eu-west-3.amazonaws.com/msg-devops-frontend:latest
```

### 5. Mettre à jour les manifests

Remplacer `<AWS_ACCOUNT_ID>` dans les fichiers :
- `k8s/backend-deployment.yaml`
- `k8s/frontend-deployment.yaml`

```bash
# Automatique avec sed
cd k8s
sed -i "s/<AWS_ACCOUNT_ID>/$AWS_ACCOUNT_ID/g" backend-deployment.yaml
sed -i "s/<AWS_ACCOUNT_ID>/$AWS_ACCOUNT_ID/g" frontend-deployment.yaml
sed -i "s/<AWS_REGION>/eu-west-3/g" backend-deployment.yaml
sed -i "s/<AWS_REGION>/eu-west-3/g" frontend-deployment.yaml
```

### 6. Déployer l'application

```bash
kubectl apply -f k8s/backend-deployment.yaml
kubectl apply -f k8s/backend-service.yaml
kubectl apply -f k8s/frontend-deployment.yaml
kubectl apply -f k8s/frontend-service.yaml
```

### 7. Obtenir l'URL du LoadBalancer

```bash
kubectl get service frontend
```

Attendre que `EXTERNAL-IP` passe de `<pending>` à une URL (peut prendre 2-5 minutes).

---

## Configuration Terraform

### Fichier principal : infra/main.tf

```hcl
# Repositories ECR
resource "aws_ecr_repository" "backend" {
  name = "msg-devops-backend"
}

resource "aws_ecr_repository" "frontend" {
  name = "msg-devops-frontend"
}

# VPC
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  # Configuration VPC...
}

# Cluster EKS
module "eks" {
  source = "./eks"
  # Configuration EKS...
}
```

### Outputs Terraform

```bash
# Afficher les outputs après apply
terraform output

# Exemples d'outputs
eks_cluster_endpoint
ecr_backend_repository_url
ecr_frontend_repository_url
```

---

## Configuration CI/CD avec GitHub Actions

### 1. Configurer les secrets GitHub

Naviguez vers : `https://github.com/YOUR_USERNAME/msg_devops/settings/secrets/actions`

Ajoutez les secrets suivants :

| Secret | Valeur |
|--------|--------|
| `AWS_ACCESS_KEY_ID` | Votre AWS Access Key |
| `AWS_SECRET_ACCESS_KEY` | Votre AWS Secret Key |

### 2. Workflow GitHub Actions

Le workflow est déjà configuré dans `.github/workflows/ci-cd.yaml` :

**Déclenchement** : À chaque push sur `main`

**Étapes** :
1. Checkout du code
2. Configuration AWS
3. Login à ECR
4. Build des images Docker
5. Push vers ECR
6. Mise à jour des manifests Kubernetes
7. Déploiement sur EKS

### 3. Vérifier le workflow

Après un push sur `main` :
1. Aller dans l'onglet **Actions** de votre repository GitHub
2. Vérifier que le workflow s'exécute correctement
3. Consulter les logs en cas d'erreur

---

## Commandes utiles

### Gestion du cluster EKS

```bash
# Lister les clusters
aws eks list-clusters --region eu-west-3

# Décrire le cluster
aws eks describe-cluster --name msg-devops-cluster --region eu-west-3

# Mettre à jour kubeconfig
aws eks update-kubeconfig --region eu-west-3 --name msg-devops-cluster

# Vérifier la connexion
kubectl get nodes
```

### Gestion des images ECR

```bash
# Lister les repositories
aws ecr describe-repositories --region eu-west-3

# Lister les images d'un repository
aws ecr list-images --repository-name msg-devops-backend --region eu-west-3

# Supprimer une image
aws ecr batch-delete-image \
    --repository-name msg-devops-backend \
    --image-ids imageTag=latest \
    --region eu-west-3
```

### Gestion des pods et services

```bash
# Lister les pods
kubectl get pods

# Logs d'un pod
kubectl logs <pod-name>

# Détails d'un pod
kubectl describe pod <pod-name>

# Lister les services
kubectl get services

# Obtenir l'URL du LoadBalancer
kubectl get service frontend -o jsonpath='{.status.loadBalancer.ingress[0].hostname}'
```

### Scaling

```bash
# Scaler le frontend
kubectl scale deployment/frontend --replicas=3

# Vérifier le scaling
kubectl get deployments
```

**Note** : Le backend reste à 1 replica à cause de SQLite.

---

## Coûts AWS

### Estimation mensuelle

| Ressource | Configuration | Coût mensuel (EUR) |
|-----------|---------------|-------------------|
| EKS Control Plane | - | ~73 EUR |
| EC2 Instances | 2x t3.medium | ~60 EUR |
| NAT Gateway | 1 instance | ~40 EUR |
| Load Balancer | ALB | ~15 EUR |
| EBS Volumes | 20 GB per node | ~4 EUR |
| Data Transfer | ~100 GB | ~5 EUR |
| **Total** | - | **~190 EUR** |

### Optimisation des coûts

**Pour réduire les coûts** :
1. Utiliser des instances Spot pour les nodes (~70% moins cher)
2. Arrêter le cluster pendant les périodes d'inactivité
3. Utiliser une seule instance t3.small au lieu de 2x t3.medium
4. Détruire l'infrastructure après les tests

**IMPORTANT** : Détruire l'infrastructure après utilisation pour éviter les frais :

```bash
./deploy-eks-oneclick.sh --destroy
```

---

## Monitoring et logs

### CloudWatch Logs

Les logs EKS sont automatiquement envoyés vers CloudWatch.

```bash
# Consulter les logs via AWS CLI
aws logs tail /aws/eks/msg-devops-cluster/cluster --follow --region eu-west-3
```

### Kubernetes Dashboard

```bash
# Installer le dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

# Créer un token
kubectl -n kubernetes-dashboard create token admin-user

# Port-forward
kubectl proxy

# Accéder à http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

### Metrics avec kubectl

```bash
# Installer metrics-server
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

# Voir les métriques
kubectl top nodes
kubectl top pods
```

---

## Débogage

### Problème : Pods en CrashLoopBackOff

```bash
# Vérifier les logs
kubectl logs <pod-name>

# Vérifier les événements
kubectl get events --sort-by=.metadata.creationTimestamp

# Décrire le pod
kubectl describe pod <pod-name>
```

### Problème : Images ECR non accessibles

```bash
# Vérifier que les images existent
aws ecr list-images --repository-name msg-devops-backend --region eu-west-3

# Vérifier les permissions IAM du node
kubectl describe pod <pod-name> | grep -A 10 Events
```

### Problème : LoadBalancer en pending

```bash
# Vérifier le service
kubectl describe service frontend

# Vérifier les événements AWS
aws elbv2 describe-load-balancers --region eu-west-3
```

### Problème : Terraform state locked

```bash
cd infra
terraform force-unlock <LOCK_ID>
```

---

## Mise à jour de l'application

### Via CI/CD (Recommandé)

```bash
# 1. Modifier le code
# 2. Commiter et pousser
git add .
git commit -m "Update application"
git push origin main

# GitHub Actions déploiera automatiquement
```

### Manuellement

```bash
# 1. Builder et pousser les nouvelles images
./deploy-eks-oneclick.sh --skip-terraform

# 2. Forcer le redéploiement
kubectl rollout restart deployment/backend
kubectl rollout restart deployment/frontend

# 3. Vérifier le rollout
kubectl rollout status deployment/backend
kubectl rollout status deployment/frontend
```

---

## Nettoyage

### Supprimer l'application

```bash
kubectl delete -f k8s/
```

### Détruire l'infrastructure

```bash
# Avec le script
./deploy-eks-oneclick.sh --destroy

# Ou manuellement
cd infra
terraform destroy
```

**ATTENTION** : Cette opération est irréversible et supprimera toutes les ressources AWS créées.

---

## Haute disponibilité et scalabilité

### Limites actuelles

| Composant | Limitation | Raison |
|-----------|------------|--------|
| Backend | 1 replica | SQLite (accès concurrent limité) |
| Database | PVC local | Pas de réplication |

### Recommandations pour la production

Pour une véritable production haute disponibilité :

1. **Migrer vers RDS PostgreSQL**
   - Haute disponibilité Multi-AZ
   - Backups automatiques
   - Réplication
   - Coût additionnel : ~15-60 EUR/mois

2. **Augmenter les replicas backend**
   - Après migration vers RDS : `replicas: 3`

3. **Configurer l'autoscaling**
   ```bash
   kubectl autoscale deployment frontend --cpu-percent=70 --min=2 --max=10
   ```

4. **Utiliser un domaine custom**
   - Route 53 pour le DNS
   - ACM pour les certificats SSL
   - ALB avec HTTPS

Consultez **SQLITE_NOTES.md** pour plus de détails sur la migration.

---

## Comparaison avec Minikube

| Aspect | Minikube | EKS |
|--------|----------|-----|
| Coût | Gratuit | ~190 EUR/mois |
| Durée setup | ~5 minutes | ~20-30 minutes |
| Environnement | Local | Cloud AWS |
| Scaling | Limité | Illimité |
| Haute disponibilité | Non | Oui (avec RDS) |
| Usage | Développement | Production |
| Internet requis | Oui | Oui |
| CI/CD | Non | GitHub Actions |

Pour plus de détails, consultez **DEPLOYMENT_COMPARISON.md**.

---

## Ressources supplémentaires

- **Documentation EKS** : https://docs.aws.amazon.com/eks/
- **Terraform AWS Modules** : https://registry.terraform.io/modules/terraform-aws-modules/eks/aws
- **ONECLICK_GUIDE.md** : Guide des scripts automatisés
- **SYSTEM_REQUIREMENTS.md** : Configuration système détaillée
- **SQLITE_NOTES.md** : Notes sur SQLite et migration vers RDS

---

## Support

En cas de problème :

1. Vérifier les prérequis : `./deploy-eks-oneclick.sh --check-only`
2. Consulter les logs Terraform : `cd infra && terraform show`
3. Consulter les logs Kubernetes : `kubectl logs <pod-name>`
4. Vérifier les événements : `kubectl get events`
5. Mode verbose : `./deploy-eks-oneclick.sh --verbose`

---

**Version** : 1.0.0  
**Dernière mise à jour** : Décembre 2025