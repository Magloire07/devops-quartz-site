---
title: 4-Base de Données
---

# Notes sur SQLite avec Kubernetes

## Configuration actuelle

L'application utilise **SQLite** comme base de données. Voici les points importants :

### Limitations avec SQLite sur Kubernetes

#### 1. Réplicas limités à 1

SQLite ne supporte pas les accès concurrents multiples. Le backend est donc configuré avec `replicas: 1`.

**Fichiers concernés** :

- `k8s/backend-deployment.yaml` : `replicas: 1`
- `k8s-minikube/backend-deployment.yaml` : `replicas: 1`

#### 2. PersistentVolume requis

La base de données SQLite est stockée dans un PersistentVolume pour persister les données entre les redémarrages du pod.

**Configuration** :

| Environnement | Taille | Mode d'accès  |
| ------------- | ------ | ------------- |
| EKS           | 1Gi    | ReadWriteOnce |
| Minikube      | 500Mi  | ReadWriteOnce |

#### 3. Mode ReadWriteOnce

Le volume est en mode `ReadWriteOnce`, ce qui signifie qu'il ne peut être monté que sur un seul nœud à la fois.

**Conséquence** : Pas de scaling horizontal possible avec SQLite.

---

## Implications opérationnelles

### Avantages de SQLite

| Avantage           | Description                                      |
| ------------------ | ------------------------------------------------ |
| Simplicité         | Pas de serveur de base de données séparé à gérer |
| Légèreté           | Faible empreinte mémoire                         |
| Zéro configuration | Aucune configuration de connexion requise        |
| Portable           | Fichier unique facilement sauvegardable          |

### Inconvénients de SQLite sur Kubernetes

| Inconvénient                | Impact                              |
| --------------------------- | ----------------------------------- |
| Pas de scaling horizontal   | Limité à 1 replica                  |
| Point de défaillance unique | Pas de haute disponibilité          |
| Performances limitées       | Moins performant sous charge élevée |
| Pas de réplication          | Pas de backup automatique           |

---

## Migration vers une base de données relationnelle

Pour permettre le scaling horizontal (plusieurs replicas), vous devriez migrer vers une base de données relationnelle comme :

### Options recommandées

| Base de données | Avantages                               | Inconvénients                    |
| --------------- | --------------------------------------- | -------------------------------- |
| **PostgreSQL**  | Open-source, robuste, features avancées | Configuration requise            |
| **MySQL**       | Populaire, bien documenté               | Moins de features que PostgreSQL |
| **Amazon RDS**  | Managé par AWS, backups automatiques    | Coût supplémentaire              |

---

## Guide de migration vers PostgreSQL

Pour passer à PostgreSQL, voici les modifications nécessaires :

### 1. Mettre à jour requirements.txt

```txt
psycopg2-binary==2.9.9
```

### 2. Modifier la connexion dans question_dao.py

```python
import psycopg2
import os

DB_HOST = os.getenv("DB_HOST", "db")
DB_NAME = os.getenv("DB_NAME", "quiz")
DB_USER = os.getenv("DB_USER", "admin")
DB_PASS = os.getenv("DB_PASS", "changeme123")

def get_connection():
    return psycopg2.connect(
        host=DB_HOST,
        database=DB_NAME,
        user=DB_USER,
        password=DB_PASS
    )
```

### 3. Déployer PostgreSQL sur Kubernetes

Décommenter les fichiers suivants :

- `k8s/db-deployment.yaml`
- `k8s/db-service.yaml`

**Note** : Ces fichiers sont actuellement commentés car l'application utilise SQLite.

### 4. Mettre à jour le backend-deployment.yaml

Ajouter les variables d'environnement :

```yaml
env:
  - name: DB_HOST
    value: "db"
  - name: DB_NAME
    value: "quiz"
  - name: DB_USER
    value: "admin"
  - name: DB_PASS
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: password
```

### 5. Créer un Secret pour le mot de passe

```bash
kubectl create secret generic db-secret \
  --from-literal=password=changeme123
```

### 6. Augmenter les replicas

Après migration vers PostgreSQL, vous pouvez augmenter les replicas :

```yaml
spec:
  replicas: 3 # Au lieu de 1
```

---

## Configuration avec Amazon RDS (Production)

Pour un environnement de production sur AWS EKS, utilisez Amazon RDS :

### Avantages d'Amazon RDS

- Backups automatiques quotidiens
- Haute disponibilité avec Multi-AZ
- Mises à jour automatiques
- Monitoring intégré
- Scaling vertical facile

### Étapes pour configurer RDS

#### 1. Créer une instance RDS via Terraform

Ajouter dans `infra/main.tf` :

```hcl
resource "aws_db_instance" "postgres" {
  identifier           = "msg-devops-db"
  engine              = "postgres"
  engine_version      = "15.3"
  instance_class      = "db.t3.micro"
  allocated_storage   = 20
  storage_type        = "gp2"

  db_name  = "quiz"
  username = "admin"
  password = var.db_password

  vpc_security_group_ids = [aws_security_group.rds.id]
  db_subnet_group_name   = aws_db_subnet_group.main.name

  skip_final_snapshot = true
  publicly_accessible = false

  tags = {
    Name = "msg-devops-postgres"
  }
}

output "rds_endpoint" {
  value = aws_db_instance.postgres.endpoint
}
```

#### 2. Configurer le backend pour utiliser RDS

Mettre à jour les variables d'environnement dans `k8s/backend-deployment.yaml` :

```yaml
env:
  - name: DB_HOST
    value: "RDS_ENDPOINT" # Remplacer par l'output Terraform
  - name: DB_NAME
    value: "quiz"
  - name: DB_USER
    value: "admin"
  - name: DB_PASS
    valueFrom:
      secretKeyRef:
        name: rds-secret
        key: password
```

#### 3. Estimer les coûts RDS

| Instance     | Coût mensuel (EUR) |
| ------------ | ------------------ |
| db.t3.micro  | ~15 EUR            |
| db.t3.small  | ~30 EUR            |
| db.t3.medium | ~60 EUR            |

**Total avec EKS** : ~205-250 EUR/mois (au lieu de ~190 EUR/mois)

---

## Stratégie de backup avec SQLite

Voici comment sauvegarder SQLite :

### Backup manuel

```bash
# Se connecter au pod backend
kubectl exec -it deployment/backend -- sh

# Copier la base de données
cp /app/data/quiz.db /tmp/quiz-backup-$(date +%Y%m%d).db

# Sortir du pod et copier localement
exit
kubectl cp backend-pod-name:/tmp/quiz-backup-20251215.db ./quiz-backup.db
```

### Backup automatisé (CronJob Kubernetes)

Créer un fichier `k8s/backup-cronjob.yaml` :

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: sqlite-backup
spec:
  schedule: "0 2 * * *" # Tous les jours à 2h du matin
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: backup
              image: busybox
              command:
                - /bin/sh
                - -c
                - cp /data/quiz.db /backup/quiz-$(date +%Y%m%d).db
              volumeMounts:
                - name: sqlite-storage
                  mountPath: /data
                - name: backup-storage
                  mountPath: /backup
          restartPolicy: OnFailure
          volumes:
            - name: sqlite-storage
              persistentVolumeClaim:
                claimName: sqlite-pvc
            - name: backup-storage
              persistentVolumeClaim:
                claimName: backup-pvc
```

### Checklist de migration

- [ ] Mettre à jour `requirements.txt`
- [ ] Modifier `question_dao.py`
- [ ] Déployer PostgreSQL (pod ou RDS)
- [ ] Créer les Secrets Kubernetes
- [ ] Mettre à jour les variables d'environnement
- [ ] Augmenter les replicas backend
- [ ] Tester la connexion
- [ ] Migrer les données existantes
- [ ] Supprimer le PVC SQLite

---

## Documentation complémentaire

- **PostgreSQL sur Kubernetes** : https://kubernetes.io/docs/tasks/run-application/run-replicated-stateful-application/
- **Amazon RDS** : https://aws.amazon.com/rds/postgresql/
- **psycopg2 Documentation** : https://www.psycopg.org/docs/

---

**Version** : 1.0.0  
**Dernière mise à jour** : Décembre 2025
