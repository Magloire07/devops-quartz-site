---
title: Projet Unity – Scène immersive
authors: CISS Aissatou & MZYENE Assia
filiere: E4FI
annee_universitaire: 2025-2026
ecole: ESIEE
---

## Informations générales

**Noms / Prénoms :** CISS Aissatou & MZYENE Assia  
**Filière :** E4FI  
**Année universitaire :** 2025–2026  
**École :** ESIEE  

🎥 **Vidéo du jeu :**  
https://drive.google.com/file/d/1yztImPr_P3YZUTY-GAGN_MoU1i4dsKQV/view?usp=sharing

---

## Sommaire

1. Présentation du projet  
2. Intention artistique et narrative  
3. Description de la scène  
4. Éclairage dynamique  
5. Système de particules (poussière)  
6. Shader de silhouettes abstraites (Ray Marching)  
7. Post-processing  
8. Optimisation et performances  
9. Conclusion  
10. Ressources  

---

## 1. Présentation du projet

Ce projet consiste en la création d’une scène 3D immersive réalisée sous **Unity**, explorant des techniques avancées de rendu en temps réel.

L’objectif principal est de proposer une expérience visuelle et atmosphérique forte, reposant sur la narration environnementale et l’utilisation d’effets graphiques avancés.

Le projet s’inscrit dans un contexte d’enquête, autour de la disparition inexpliquée d’une femme, sans recourir à une narration explicite ou à des dialogues.

---

## 2. Intention artistique et narrative

L’intention artistique est de suggérer une présence absente à travers l’environnement plutôt que de la représenter directement.  
La scène cherche à instaurer un sentiment de malaise et d’incertitude, en laissant le joueur interpréter les éléments visuels présents.

Le lieu choisi (une salle d’archives) symbolise la mémoire, les traces du passé et les informations incomplètes, en lien direct avec le thème de la disparition.

---

## 3. Description de la scène

La scène se déroule dans une salle d’archives intérieure, composée d’étagères et de dossiers.

La caméra est fixe afin de renforcer le sentiment d’observation et d’impuissance face aux événements suggérés.

La narration repose principalement sur :
- la lumière,
- l’atmosphère,
- les effets visuels,
- l’absence de présence humaine explicite.

---

## 4. Éclairage dynamique

Un système d’éclairage narratif a été mis en place afin de guider le regard du joueur et renforcer l’ambiance anxiogène.

Certaines lampes présentent un comportement instable, simulé par des variations aléatoires de leur intensité lumineuse.  
Ce clignotement subtil suggère un environnement vieillissant et mal entretenu, tout en accentuant la tension de la scène.

L’éclairage met également en valeur les éléments narratifs clés afin de capter l’attention du joueur.

---

## 5. Système de particules — Poussière

Un système de particules simule la présence de poussière en suspension dans l’air de la salle d’archives.

Les particules :
- se déplacent lentement,
- sont plus visibles dans les zones éclairées,
- bougent subtilement avec le joueur.

Cet effet contribue à donner une sensation de lieu abandonné et figé dans le temps, tout en ajoutant de la profondeur visuelle à la scène.

---

## 6. Shader de silhouettes abstraites (Ray Marching)

Un shader personnalisé utilisant une approche simplifiée de **ray marching** a été développé afin de générer une silhouette humaine abstraite.

Cette silhouette :
- n’est jamais totalement visible,
- apparaît de manière floue et instable,
- suggère une présence sans la représenter explicitement.

Une animation procédurale légère contrôle son opacité et sa position verticale, donnant l’impression d’une présence « vivante » mais incertaine.

Ce choix renforce directement le thème de la disparition et le sentiment de doute ressenti par le joueur.

---

## 7. Post-processing

Une chaîne de post-processing a été mise en place à l’aide du pipeline **URP** afin d’unifier visuellement la scène.

Les effets utilisés incluent :
- un color grading froid (teintes bleutées),
- un bloom léger sur les sources lumineuses,
- une vignette subtile pour focaliser le regard,
- un grain léger pour ajouter une texture plus organique à l’image.

Ces effets sont volontairement discrets afin de servir l’ambiance sans détourner l’attention du décor.

---

## 8. Optimisation et performances

Une attention particulière a été portée aux performances du rendu.

Les optimisations mises en place incluent :
- l’utilisation de Level of Detail (LOD) sur certains objets,
- un nombre limité de sources lumineuses dynamiques,
- des systèmes de particules peu coûteux.

Ces choix permettent de maintenir une performance stable tout en conservant une qualité visuelle élevée.

---

## 9. Conclusion

Ce projet propose une expérience visuelle immersive reposant sur la narration environnementale et l’utilisation d’effets graphiques avancés sous Unity.

Plutôt que de montrer explicitement les événements, la scène suggère une histoire à travers l’atmosphère, la lumière et des éléments visuels abstraits.

L’ensemble des techniques mises en œuvre vise à renforcer le thème de la disparition, en laissant volontairement place à l’interprétation du joueur.

---

## 10. Ressources

### Assets

- https://skfb.ly/oqIoP  
  **Utilisation :** Décor principal de la salle d’archives

- https://skfb.ly/oMPO7  
  **Utilisation :** Personnage principal

### Matériaux

Les matériaux et textures utilisés proviennent des ressources natives de Unity.

### Shaders & Références techniques

**Ray Marching**
- Articles et tutoriels généraux sur le ray marching (adaptation personnelle)
- Shader développé et simplifié pour le projet

🔗 Code du shader :  
https://www.notion.so/Code-du-shader-2de1d8db31f781c5a3d6dfeeb0a65efb?pvs=21

### Scripts

- `FlickeringLight` : variation d’intensité lumineuse  
- `SilhouettePulse` : animation procédurale des silhouettes  
- `Interactable` : interaction avec les objets  
- `InvestigationManager` : suivi de l’avancée du jeu  
- `PlayerMovement` : gestion des déplacements du joueur  

### Outils & Logiciels

- **Unity** (URP)  
- **Visual Studio** (C# / shaders)  
- **Notion** (documentation)
