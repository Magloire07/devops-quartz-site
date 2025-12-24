---
title: 2-Minikude deploy
---

# Guide de Déploiement Minikube

## Vue d'ensemble

Ce guide décrit le déploiement de l'application msg-devops sur un cluster Kubernetes local avec Minikube.

**Environnement** : Développement local  
**Coût** : Gratuit  
**Durée d'installation** : ~5 minutes

---

## Utilisation du script ONE-CLICK

### Déploiement rapide

```bash
sudo ./deploy-minikube-oneclick.sh

# Undeploy (supprimer le déploiement)
sudo ./deploy-minikube-oneclick.sh --undeploy
```

Le script gère automatiquement :

- Installation de Docker, kubectl, Minikube
- Démarrage du cluster Minikube
- Build des images Docker localement
- Déploiement de l'application
- Affichage des URLs d'accès

### Options avancées

```bash
# Avec plus de ressources
sudo ./deploy-minikube-oneclick.sh --memory 8192 --cpus 4

# Skip l'installation si déjà fait
sudo ./deploy-minikube-oneclick.sh --skip-install

# Mode verbose pour débogage
sudo ./deploy-minikube-oneclick.sh --verbose

# Mettre à jour après modification du code
sudo ./deploy-minikube-oneclick.sh --update backend   # Backend uniquement

sudo ./deploy-minikube-oneclick.sh --update frontend  # Frontend uniquement

sudo ./deploy-minikube-oneclick.sh --update all       # Les deux

# Voir les pods backend avec leurs logs
sudo ./deploy-minikube-oneclick.sh --pods backend

# Voir les pods frontend avec leurs logs
sudo ./deploy-minikube-oneclick.sh --pods frontend

# Voir tous les pods
sudo ./deploy-minikube-oneclick.sh --pods all
```

Pour plus de détails, consultez **ONECLICK_GUIDE.md**.

---

## Architecture du déploiement

### Composants déployés

| Composant  | Type             | Replicas | Port |
| ---------- | ---------------- | -------- | ---- |
| Backend    | Deployment       | 1        | 5000 |
| Frontend   | Deployment       | 2        | 8080 |
| SQLite PVC | PersistentVolume | -        | -    |

### Services exposés

| Service  | Type     | Port externe | Port interne |
| -------- | -------- | ------------ | ------------ |
| backend  | NodePort | 30500        | 5000         |
| frontend | NodePort | 30080        | 8080         |

### Images Docker

| Image               | Tag    | Source      |
| ------------------- | ------ | ----------- |
| msg-devops-backend  | latest | Build local |
| msg-devops-frontend | latest | Build local |

**Note** : Les images sont buildées localement avec `imagePullPolicy: Never` (pas de Docker Hub).

---

## Prérequis système

### Système d'exploitation

- **Linux uniquement** (Ubuntu/Debian recommandé)
- Windows et macOS non supportés

### Ressources matérielles

| Ressource | Minimum     | Recommandé  |
| --------- | ----------- | ----------- |
| RAM       | 4 GB        | 8 GB        |
| CPUs      | 2           | 4           |
| Disque    | 20 GB libre | 40 GB libre |

### Logiciels

Les outils suivants seront installés automatiquement par le script si manquants :

- Docker
- kubectl
- Minikube
- conntrack
- socat

### Permissions

- **sudo requis** pour l'installation et le démarrage de Minikube

---

## Déploiement manuel (sans script)

Si vous préférez déployer manuellement, suivez ces étapes :

### 1. Installer les dépendances

#### Docker

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

#### kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

#### Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### 2. Démarrer Minikube

```bash
minikube start --memory=4096 --cpus=2 --driver=docker
```

### 3. Builder les images localement

```bash
# Configurer Docker pour utiliser le daemon de Minikube
eval $(minikube docker-env)

# Builder le backend
cd backend
docker build -t msg-devops-backend:latest .

# Builder le frontend
cd ../frontend
docker build -t msg-devops-frontend:latest .
```

### 4. Déployer l'application

```bash
# Appliquer les manifests Kubernetes
kubectl apply -f k8s-minikube/backend-deployment.yaml
kubectl apply -f k8s-minikube/backend-service.yaml
kubectl apply -f k8s-minikube/frontend-deployment.yaml
kubectl apply -f k8s-minikube/frontend-service.yaml
```

### 5. Vérifier le déploiement

```bash
# Vérifier les pods
kubectl get pods

# Vérifier les services
kubectl get services
```

### 6. Accéder à l'application

```bash
# Obtenir l'IP de Minikube
minikube ip

# Frontend : http://<MINIKUBE_IP>:30080
# Backend : http://<MINIKUBE_IP>:30500
```

---

## Mise à jour après modification du code

### Avec le script ONE-CLICK (Recommandé)

```bash
# Après avoir modifié le code backend
sudo ./deploy-minikube-oneclick.sh --update backend

# Après avoir modifié le code frontend
sudo ./deploy-minikube-oneclick.sh --update frontend

# Mettre à jour les deux
sudo ./deploy-minikube-oneclick.sh --update all
```

### Manuellement

```bash
# 1. Configurer Docker pour Minikube
eval $(minikube docker-env)

# 2. Rebuilder l'image modifiée
cd backend  # ou frontend
docker build -t msg-devops-backend:latest .

# 3. Redémarrer le deployment
kubectl rollout restart deployment/backend

# 4. Vérifier le rollout
kubectl rollout status deployment/backend
```

---

## Configuration des manifests

### backend-deployment.yaml

Fichier : `k8s-minikube/backend-deployment.yaml`

**Points clés** :

- `replicas: 1` (limitation SQLite)
- `imagePullPolicy: Never` (image locale)
- PersistentVolumeClaim de 500Mi pour SQLite

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: msg-devops-backend:latest
          imagePullPolicy: Never
          ports:
            - containerPort: 5000
          volumeMounts:
            - name: sqlite-storage
              mountPath: /app/data
      volumes:
        - name: sqlite-storage
          persistentVolumeClaim:
            claimName: sqlite-pvc
```

### frontend-deployment.yaml

Fichier : `k8s-minikube/frontend-deployment.yaml`

**Points clés** :

- `replicas: 2` (scalable)
- `imagePullPolicy: Never` (image locale)
- Variable d'environnement `VUE_APP_API_URL`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend
          image: msg-devops-frontend:latest
          imagePullPolicy: Never
          ports:
            - containerPort: 8080
          env:
            - name: VUE_APP_API_URL
              value: "http://backend:5000"
```

---

## Commandes utiles

### Gestion du cluster

```bash
# Démarrer Minikube
minikube start

# Arrêter Minikube
minikube stop

# Supprimer le cluster
minikube delete

# Statut du cluster
minikube status

# Obtenir l'IP du cluster
minikube ip

# Dashboard Kubernetes
minikube dashboard
```

### Gestion des pods

```bash
# Lister les pods
kubectl get pods

# Détails d'un pod
kubectl describe pod <pod-name>

# Logs d'un pod
kubectl logs <pod-name>

# Logs en temps réel
kubectl logs -f <pod-name>

# Se connecter à un pod
kubectl exec -it <pod-name> -- sh
```

### Gestion des services

```bash
# Lister les services
kubectl get services

# Détails d'un service
kubectl describe service <service-name>

# Obtenir l'URL d'un service
minikube service <service-name> --url
```

### Gestion des images

```bash
# Lister les images dans Minikube
minikube ssh docker images

# Supprimer une image
minikube ssh docker rmi <image-name>

# Reconfigurer Docker pour Minikube
eval $(minikube docker-env)
```

### Gestion du stockage

```bash
# Lister les PVC
kubectl get pvc

# Détails d'un PVC
kubectl describe pvc sqlite-pvc

# Lister les PV
kubectl get pv
```

---

## Débogage

### Problème : Pods en CrashLoopBackOff

```bash
# Vérifier les logs du pod
kubectl logs <pod-name>

# Vérifier les événements
kubectl get events --sort-by=.metadata.creationTimestamp

# Décrire le pod pour plus de détails
kubectl describe pod <pod-name>
```

### Problème : Images non trouvées

```bash
# Vérifier que Docker est configuré pour Minikube
eval $(minikube docker-env)

# Vérifier les images disponibles
docker images | grep msg-devops

# Rebuilder si nécessaire
cd backend && docker build -t msg-devops-backend:latest .
cd ../frontend && docker build -t msg-devops-frontend:latest .

# Ou utiliser le script
sudo ./deploy-minikube-oneclick.sh --update all
```

### Problème : Service non accessible

```bash
# Vérifier que le service existe
kubectl get services

# Vérifier que les pods sont prêts
kubectl get pods

# Obtenir l'URL du service
minikube service frontend --url
minikube service backend --url
```

### Problème : Pas assez de ressources

```bash
# Supprimer le cluster existant
minikube delete

# Redémarrer avec plus de ressources
minikube start --memory=8192 --cpus=4
```

### Problème : PVC en état Pending

```bash
# Vérifier les PVC
kubectl get pvc

# Vérifier les PV disponibles
kubectl get pv

# Supprimer et recréer le PVC si nécessaire
kubectl delete pvc sqlite-pvc
kubectl apply -f k8s-minikube/backend-deployment.yaml
```

---

## Mise à jour de l'application

### Après modification du code

```bash
# 1. Reconfigurer Docker pour Minikube (manuel)
eval $(minikube docker-env)

# 2. Rebuilder l'image modifiée (manuel)
cd backend  # ou frontend
docker build -t msg-devops-backend:latest .

# 3. Redémarrer le deployment (manuel)
kubectl rollout restart deployment/backend

# 4. Vérifier le rollout (manuel)
kubectl rollout status deployment/backend

# OU utiliser le script ONE-CLICK (recommandé)
sudo ./deploy-minikube-oneclick.sh --update backend
```

### Forcer le redéploiement

```bash
# Supprimer les pods (seront recréés automatiquement)
kubectl delete pod -l app=backend

# Ou redémarrer le deployment
kubectl rollout restart deployment/backend
```

---

## Nettoyage

### Supprimer l'application

```bash
# Supprimer tous les déploiements et services
kubectl delete -f k8s-minikube/
```

### Supprimer le cluster

```bash
# Supprimer complètement Minikube
minikube delete
```

### Supprimer les images Docker

```bash
# Se connecter au daemon Minikube
eval $(minikube docker-env)

# Supprimer les images
docker rmi msg-devops-backend:latest
docker rmi msg-devops-frontend:latest
```

### Désinstallation complète

```bash
# Utiliser le script ONE-CLICK
sudo ./deploy-minikube-oneclick.sh --undeploy
```

---

## Addons Minikube utiles

### Activer le dashboard

```bash
minikube addons enable dashboard
minikube dashboard
```

### Activer metrics-server

```bash
minikube addons enable metrics-server
kubectl top nodes
kubectl top pods
```

### Activer ingress

```bash
minikube addons enable ingress
```

### Lister tous les addons

```bash
minikube addons list
```

---

## Ressources supplémentaires

- **Documentation Minikube** : https://minikube.sigs.k8s.io/docs/
- **Documentation Kubernetes** : https://kubernetes.io/docs/
- **ONECLICK_GUIDE.md** : Guide des scripts automatisés
- **SYSTEM_REQUIREMENTS.md** : Configuration système détaillée
- **SQLITE_NOTES.md** : Notes sur SQLite et limitations

---

## Support

En cas de problème :

1. Consulter les logs : `kubectl logs <pod-name>`
2. Vérifier les événements : `kubectl get events`
3. Utiliser le mode verbose : `sudo ./deploy-minikube-oneclick.sh --verbose`
4. Consulter le dashboard : `minikube dashboard`

---

**Version** : 1.0.0  
**Dernière mise à jour** : Décembre 2025
