---
title: chap2_TD2
tags:
  - unity
  - infographie
  - rendu
  - procedural
---

# Bruit de Perlin

Le **bruit de Perlin** est une fonction mathématique utilisée pour générer un **bruit naturel**, plus doux et cohérent qu’un bruit aléatoire brut.  
Il est largement utilisé dans les jeux vidéo pour créer des **textures**, des **terrains**, des **mouvements** ou des **effets visuels réalistes**.

---

## Bruit de Perlin dans Unity

Dans Unity, le bruit de Perlin est accessible via la fonction suivante :

```csharp
Mathf.PerlinNoise(xCoord, yCoord) // renvoie une valeur comprise entre 0 et 1 selon les coordonnées fournies
```

### Remarques
- Unity intègre uniquement le **bruit de Perlin en 2D**.
- Pour générer du bruit de Perlin en **3D**, il est nécessaire de coder sa propre implémentation ou d’utiliser une bibliothèque externe (par exemple un script open source disponible sur GitHub).

---

## Génération d’une texture avec le bruit de Perlin

Pour générer une texture procédurale, on parcourt chaque pixel de la texture et on calcule une valeur de bruit de Perlin à partir de ses coordonnées.

Chaque pixel est ensuite coloré en fonction de cette valeur, généralement à l’aide de **nuances de gris**.

### Résultat

On obtient une **texture naturelle et cohérente**, idéale pour représenter :
- des terrains,
- des nuages,
- des surfaces rocheuses,
- ou d’autres éléments organiques.

---

## Script Bobbling.cs

Le script **Bobbling.cs** illustre l’utilisation du bruit de Perlin pour animer un objet.

Il permet de créer un mouvement fluide et pseudo-aléatoire, donnant l’impression que l’objet **flotte doucement**, sans mouvements brusques.

---

## Questions / Réponses

### Qu’est-ce que le bruit de Perlin ?
**Réponse :**  
Une fonction mathématique qui génère un bruit aléatoire cohérent, utilisée pour créer des textures ou des animations naturelles.

### Quelle fonction Unity permet d’utiliser le bruit de Perlin ?
**Réponse :**  
`Mathf.PerlinNoise(xCoord, yCoord)`

### Quelle est la plage de valeurs retournée par `Mathf.PerlinNoise` ?
**Réponse :**  
Une valeur comprise entre **0 et 1**.

### À quoi sert le script `Bobbling` ?
**Réponse :**  
À animer un objet en le faisant bouger doucement de haut en bas à l’aide du bruit de Perlin.

### Pourquoi utiliser du bruit de Perlin plutôt qu’un mouvement aléatoire pur ?
**Réponse :**  
Parce que le bruit de Perlin est **cohérent et fluide**, ce qui évite les mouvements brusques et irréalistes.
