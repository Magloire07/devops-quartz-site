---
title: TD3
---
Lab 3 : How to Deploy Your Apps
Introduction
L’objectif de ce TP était de découvrir et de mettre en pratique plusieurs méthodes de déploiement d’applications en DevOps.
 Nous avons abordé successivement :
l’orchestration de serveurs avec Ansible,


l’orchestration de machines virtuelles avec Packer et OpenTofu,


l’orchestration de conteneurs avec Docker et Kubernetes,


ainsi qu’une introduction au serverless avec AWS Lambda.


Section 1 – Orchestration de serveurs avec Ansible
Création des instances EC2
Dans un premier temps, nous avons configuré Ansible afin de pouvoir interagir avec AWS.
 Les credentials AWS ont été configurés via l’AWS CLI.
Nous avons ensuite créé un fichier de variables afin de déployer plusieurs instances EC2 :
3 instances pour l’application Node.js


1 instance dédiée au load balancer Nginx


La commande ansible-playbook permet de lancer la création des instances EC2 automatiquement.
 Après exécution, les instances sont bien visibles dans la console AWS avec les tags définis.

Inventaire dynamique
Un inventaire dynamique basé sur le plugin amazon.aws.aws_ec2 a été mis en place.
 Cela permet à Ansible de détecter automatiquement les instances EC2 selon leurs tags, sans avoir à maintenir un inventaire statique.
Des variables de groupe ont été définies pour : l’utilisateur SSH, la clé privée, la désactivation du host key checking
Déploiement de l’application Node.js
Une application Node.js simple a été déployée sur les instances :
installation de Node.js


création d’un utilisateur dédié


utilisation de PM2 pour gérer le process applicatif


L’application écoute sur le port 8080 et renvoie un message de test.
 Après déploiement, un curl sur l’IP publique des instances permet de vérifier que l’application fonctionne correctement.
Mise en place du load balancer Nginx
Une instance EC2 supplémentaire a été créée pour héberger Nginx.
 Nginx est configuré comme reverse proxy afin de répartir les requêtes entre les instances applicatives.
Le fichier de configuration est généré dynamiquement avec un template Jinja2, utilisant les IP des instances Ansible détectées.
Un test via l’IP publique de Nginx confirme que la requête est bien distribuée entre les serveurs backend.
Rolling update avec Ansible
Le mécanisme de rolling update est activé grâce au paramètre serial: 1 dans le playbook.
 Lors d’une mise à jour du code applicatif, les instances sont mises à jour une par une, sans interruption de service.
Un test en continu avec curl montre que l’application reste disponible pendant toute la mise à jour.
Section 2 – Orchestration de VM avec Packer et OpenTofu
Création d’une image avec Packer
Packer est utilisé pour créer une image AMI personnalisée contenant : Node.js, PM2, l’application Node.js prête à être lancée
La commande packer build permet de générer l’AMI automatiquement.
Déploiement avec OpenTofu
OpenTofu est utilisé pour déployer : un Auto Scaling Group, plusieurs instances basées sur l’AMI créée et un Application Load Balancer (ALB)
Les paramètres comme le nombre d’instances, le type d’instance et le port applicatif sont définis dans la configuration OpenTofu.

Rolling update avec ASG
Un mécanisme de refresh progressif des instances est mis en place via l’Auto Scaling Group.
 Lorsqu’une nouvelle AMI est utilisée, les instances sont remplacées une par une, garantissant une disponibilité continue de l’application.
Section 3 – Conteneurisation avec Docker et Kubernetes
Création de l’image Docker
Une image Docker est créée à partir d’un Dockerfile simple basé sur Node.js.
 L’image expose le port 8080 et exécute l’application Node.js au démarrage.
L’application est testée localement via Docker avec succès.
Déploiement sur Kubernetes local
Kubernetes est activé via Docker Desktop.
 Un Deployment est créé avec plusieurs replicas de l’application.
Un Service de type LoadBalancer permet d’exposer l’application localement.
 Un curl confirme que l’application est accessible via Kubernetes.
Rolling update Kubernetes
Une nouvelle version de l’image Docker est générée.
 En mettant à jour l’image dans le Deployment, Kubernetes effectue automatiquement un rolling update sans interruption.

Section 4 – Introduction au serverless avec AWS Lambda
Une fonction AWS Lambda simple est déployée via OpenTofu.
 Elle renvoie un message HTTP lorsqu’elle est appelée.
Une API Gateway est configurée pour exposer la fonction Lambda via une URL publique.
 Après déploiement, un appel HTTP permet de vérifier que la fonction fonctionne correctement.
Une modification du code montre que les mises à jour sont rapides et simples dans un environnement serverless.
Conclusion
Ce TP a permis de comparer plusieurs approches de déploiement :
-serveurs traditionnels avec Ansible
-machines virtuelles automatisées avec Packer et OpenTofu
-conteneurs avec Docker et Kubernetes
-fonctions serverless avec AWS Lambda


Chaque méthode présente des avantages selon le contexte, la complexité de l’infrastructure et les besoins de scalabilité.
 Ce TP donne une vision globale des outils DevOps utilisés dans des environnements modernes.
