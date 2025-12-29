## Mise en place du Workflow OpenTofu CI
###  Objectif
Le workflow OpenTofu CI a pour but de vérifier l’infrastructure déclarative avant tout déploiement réel. Il permet :
- De s’assurer que les fichiers Terraform / OpenTofu sont correctement formatés.
- De vérifier la syntaxe et la validité des fichiers .tf (terraform validate).
- De générer un plan (tofu plan) sans appliquer les changements, permettant de détecter les erreurs et de visualiser les actions prévues.

*ps* : ce workflow ne crée aucune ressource AWS. Il est safe pour les tests et permet d’éviter tout coût pendant la phase de développement.

### Mise en place
- *Emplacement du workflow*

Fichier : .github/workflows/opentofu.yml

- *Déclenchement :*

push ou pull_request sur le dossier infra/

 -*workflow_dispatch pour lancement manuel*

### Configuration du job OpenTofu

OS utilisé : ubuntu-latest

*Étapes principales :*

- Checkout du repo
- Installation d’OpenTofu (opentofu/setup-opentofu@v1)
- Vérification du format (tofu fmt -check)
- Initialisation de l’infrastructure (tofu init -backend=false)
- Validation des fichiers (tofu validate)
- Génération du plan (tofu plan -out=tfplan)
- Upload du plan comme artifact pour consultation
- Credentials AWS


** Utilisation de credentials fictifs (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY) pour permettre le plan safe, on mettra les vrais credentials lors du déploiement.

Ce que ça teste
- **Formatage** : assure que tous les fichiers .tf respectent le style attendu par OpenTofu.
- **Initialisation** : vérifie que tous les modules sont accessibles et correctement configurés.
- **Validation** : détection des erreurs de syntaxe et de configuration avant le déploiement.
- **Planification** : permet de voir les actions prévues (add, change, destroy) sans toucher aux ressources réelles.

### Utilité

- **Sécurité** : pas de risque de création de ressources non désirées ou de facturation accidentelle.
- **Qualité** : détecte les erreurs de syntaxe ou de configuration avant de merge dans main.
- **Collaboration** : permet à l’équipe de vérifier l’infrastructure avant déploiement et d’avoir un aperçu des modifications.
- **Automatisation** : prépare la base pour intégrer les outputs dans le workflow CI/CD (build Docker, déploiement EKS).

