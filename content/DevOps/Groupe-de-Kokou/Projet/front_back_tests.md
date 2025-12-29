# Mise en place des tests automatisés et du pipeline CI

## 1. Contexte du projet

Le projet est composé de deux parties principales :

- Backend : application Python (Flask)
- Frontend : application Vue.js (Vite)

L’objectif était de mettre en place des tests automatisés simples, intégrés dans un pipeline CI avec GitHub Actions, afin de garantir la qualité du code sans déployer d’infrastructure ni générer de coûts.

Les tests d’infrastructure (OpenTofu / Terraform) ont été traités séparément et ne sont pas inclus dans ce document.

---

## 2. Tests automatisés du backend

### 2.1 Objectifs

Les tests backend permettent de :

- Vérifier que l’application Flask démarre correctement
- Tester le bon fonctionnement des routes principales
- Détecter rapidement toute régression lors d’un changement de code

Ces tests sont volontairement simples et ne nécessitent pas :
- de base de données réelle
- de Docker
- d’accès à des services cloud

---

### 2.2 Mise en place avec pytest

Le framework pytest a été utilisé pour écrire les tests unitaires.

Structure du backend :

backend/
- app.py
- requirements.txt
- tests/
  - test_app.py

Les tests utilisent le client de test Flask afin d’envoyer des requêtes HTTP à l’application et vérifier les réponses (codes HTTP, contenu minimal).

---

### 2.3 Exécution des tests

Commande utilisée en local et en CI :

python -m pytest

L’utilisation de `python -m pytest` permet d’éviter les problèmes de PATH rencontrés sur certains environnements (notamment Windows).

---

## 3. Test automatisé du frontend (build)

### 3.1 Objectifs

Le test frontend consiste à vérifier que l’application Vue.js :

- peut être construite en mode production
- ne contient pas d’erreurs de configuration
- résout correctement les imports et alias (@/)

Ce test ne valide pas l’interface visuelle, mais garantit que l’application est déployable.

---

### 3.2 Commande utilisée

Commande exécutée en local et en CI :

npm run build

Cette commande utilise Vite et Rollup pour générer le bundle de production.

---

### 3.3 Problèmes rencontrés et corrections

#### Alias @ non résolu

Le build échouait car l’alias `@` pointant vers le dossier `src` avait été supprimé dans la configuration Vite.

Correction :
- Réintroduction de l’alias `@` dans `vite.config.js`

Cela a corrigé les erreurs du type :
- impossible de résoudre `@/views/AdminPage.vue`

---

#### Problème lié aux DevTools Vue

Un autre problème venait de l’activation des Vue DevTools pendant le build, provoquant des erreurs liées à `localStorage` dans un contexte Node.js.

Correction :
- Activation des DevTools uniquement en mode développement
- Désactivation automatique en mode production

---

## 4. Pipeline CI avec GitHub Actions

### 4.1 Objectif du pipeline

Le pipeline CI a pour but de :

- Vérifier automatiquement la qualité du backend via pytest
- Vérifier que le frontend peut être buildé
- Bloquer les erreurs avant le merge sur la branche principale

Aucune action de déploiement ou d’application d’infrastructure n’est réalisée.

---

### 4.2 Organisation des jobs

Le pipeline est composé de deux jobs principaux :

1. Backend tests  
   - Installation de Python
   - Installation des dépendances
   - Exécution des tests pytest

2. Frontend build  
   - Installation de Node.js
   - Installation des dépendances npm
   - Build de l’application Vue.js

Le job frontend dépend du succès du job backend.

---

## 5. Lien entre les tests locaux et la CI

Les commandes utilisées dans GitHub Actions sont strictement identiques à celles utilisées en local :

- python -m pytest
- npm run build

Cela garantit que :
- un code qui passe en local passera en CI
- un échec en CI est reproductible localement

---

## 6. Bénéfices obtenus

Grâce à ces tests automatisés :

- Les erreurs sont détectées avant le merge
- Le projet gagne en fiabilité
- Le pipeline CI est rapide et peu coûteux
