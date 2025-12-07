---
title: Projet
---

# PROJET DEVOPS

**Repos**

[](https://github.com/Secr3ts/msg_devops.git)

**Cahier des charges**

[final-project-devops-for-swe-2025.pdf](images/final-project-devops-for-swe-2025.pdf)

<aside>
📖

# Résumé du service

</aside>

Mini jeu, avec scoreboard intégré qui se reset tout les 24h.

Chaque participant qui complète le jeu peut ajouter son temps au scoreboard en ajoutant son username.

### Partie Front: (OUNISSA)

Framework: [https://vite.dev/guide/](https://vite.dev/guide/)

### Partie Back: (AISSATOU & ALOIS)

Langage:

### Partie Container: (KOKOU)

## Répartition des TDs

- Aissatou : td3
- Ounissa : td4
- Kokou : td5
- Aloïs : td6

<aside>
✍🏽

**TD5**

- Compte rendu
  Succès du test d’échec dans le work app-tests.yml qui exécute le fichier app.test.js pour s’assurer que le end-point get / retourne bien “hello work”.
  Contenu initiale and app.js
  ```bash
  app.get('/', (req, res) => {
    res.send('Hello, World!');
  });
  ```
  Contenu qui fait échoué le test
  ```bash
  app.get('/', (req, res) => {
    res.send('DevOps Labs!');
  });
  ```
  Le test dans app.tests.yml
  ```bash
  expect(response.text).toBe('Hello, World!');
  ```
  Il faut donc pour que le test passe faire dans app.tests.yml
  ```bash
  expect(response.text).toBe('DevOps Labs!');
  ```
  ![Screenshot from 2025-11-01 00-33-19.png](images/Screenshot_from_2025-11-01_00-33-19.png)
  Les trois rôles IAM pour le CD Github vers AWS (tests, plan apply)
  ![Screenshot from 2025-11-04 14-42-24.png](images/Screenshot_from_2025-11-04_14-42-24.png)
  Création de Bucket S3 (kokous-bucket)
  ![Screenshot from 2025-11-04 14-41-33.png](images/Screenshot_from_2025-11-04_14-41-33.png)
  Succès des tests finaux du pipeline CI/CD après merge avec main
  ![Screenshot from 2025-11-02 20-03-18.png](images/Screenshot_from_2025-11-02_20-03-18.png)
  Lien vers le repos
  [GitHub - Magloire07/td5](https://github.com/Magloire07/td5.git)

</aside>

Questions au prof :

- Est ce que tous les TDs sont à rendre
- Est ce que tous les membres doivent faire tous les tds à livrer
