---
title: TD5
---

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

