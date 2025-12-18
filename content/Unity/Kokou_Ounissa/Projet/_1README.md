---
title: 1 - Vue d'ensemble
---
##Demo

<video width="640" controls>
  <source src="../../../../videos/demov1.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>


Tiny Flight Simulator est un simulateur de vol développé sous Unity, offrant une expérience de vol immersive avec génération procédurale de l'univers, système météorologique dynamique, et système de missions narratives.

### Objectifs du projet

Le projet vise à créer un simulateur de vol accessible combinant:

- Une physique aéronautique réaliste
- Une génération procédurale de monde ouvert
- Un système météorologique dynamique avec effets visuels avancés
- Un système de missions scénarisées
- Des graphismes optimisés avec techniques de rendu avancées

## Structure de la documentation

Cette documentation est organisée en plusieurs fichiers pour faciliter la navigation:

| Document | Description |
|----------|-------------|
| [USER_MANUAL.md](./_8USER_MANUAL.md) | Manuel utilisateur complet avec guide de prise en main |
| [ARCHITECTURE.md](./_2ARCHITECTURE.md) | Architecture technique du système |
| [MISSION_SYSTEM.md](./_3MISSION_SYSTEM.md) | Documentation du système de missions |
| [WEATHER_SYSTEM.md](./_5WEATHER_SYSTEM.md) | Système météorologique dynamique |
| [RENDERING.md](./_4RENDERING.md) | Systèmes de rendu avancés (Ray Marching, nuages volumétriques) |
| [DEVELOPER_GUIDE.md](./_8DEVELOPER_GUIDE.md) | Guide pour les développeurs |
| [TESTING.md](./_7TESTING.md) | Procédures de test et validation |

## Fonctionnalités principales

### Système de vol

- Modèle aérodynamique réaliste avec portance, traînée, et décrochage
- Contrôles souris/clavier intuitifs
- Vues cockpit et externe avec transition fluide
- Turbulences atmosphériques dynamiques
- Effets de sol (ground effect)

### Génération procédurale

- Terrain généré algorithmiquement avec système d'érosion
- Placement intelligent des aéroports et zones d'intérêt
- Optimisation de la distance de rendu
- Système de LOD (Level of Detail) automatique

### Système météorologique

- Paramètres météo ajustables en temps réel
- Effets de pluie, orage, brouillard
- Système de vent avec turbulences
- Sons ambiants adaptatifs
- Éclairage dynamique jour/nuit
- Système de nuages volumétriques

### Système de missions

Deux modes de jeu disponibles:

1. **Escape the Hell** - Mission narrative avec atmosphère horrifique
2. **Free Ride** - Mode libre sans contraintes

Voir [MISSION_SYSTEM.md](./_6MISSION_SYSTEM.md) pour plus de détails.



### Assets de Unity asset store

| Package | Utilisation |
|---------|-------------|
| Pandazole_ultimate_pack | villes et champs |

### Algorithmes clés

- **Perlin Noise** - Génération de terrain
- **Hydraulic Erosion** - Simulation d'érosion réaliste
- **Ray Marching** - Rendu volumétrique des nuages
- **FBM (Fractional Brownian Motion)** - Textures procédurales

## Architecture du projet

```
Assets/
├── _Scenes/               # Scènes Unity
│   ├── MainMenu          # Menu principal
│   └── Flight Demo       # Scène de vol
├── Scripts/              # Scripts C#
│   ├── Core/            # Systèmes principaux
│   ├── UI/              # Interface utilisateur
│   ├── Terrain/         # Génération procédurale
│   └── 3rd Party/       # Bibliothèques externes
├── Materials/           # Matériaux et shaders
├── Models/             # Modèles 3D
├── Resources/          # Ressources dynamiques
└── Shaders/           # Shaders personnalisés
```

Voir [ARCHITECTURE.md](./_2ARCHITECTURE.md) pour une description détaillée.


## Démarrage rapide

### Installation pour développeurs

```powershell
# Cloner le dépôt (si applicable)
git clone https://github.com/Magloire07/tiny-flight-simulator-beta
cd tiny-flight-simulator-beta

# Ouvrir le projet dans Unity Hub
# File > Open Project > Sélectionner le dossier
```

### Lancement du jeu

1. Ouvrir Unity
2. Charger la scène `MainMenu` dans `Assets/_Scenes/`
3. Cliquer sur Play
4. Sélectionner une mission et un avion
5. Cliquer sur "JOUER"

Voir [USER_MANUAL.md](./_8USER_MANUAL.md) pour les contrôles détaillés.

## Captures d'écran

### Menu principal

![Menu principal](images/main_menu.png)


### Menu Avions
![Menu principal](images/avion1.png)
![Menu principal](images/avion2.png)
![Menu principal](images/avion3.png)


### Vol en conditions normales

![Vol normal](images/normal_flight.png)

### Mission "Escape the Hell"

![Mission Escape the Hell](images/mission_escape_hell1.png)
![Mission Escape the Hell](images/mission_escape_hell2.png)


## Vidéos de démonstration



## État d'implémentation

### Fonctionnalités complètes

- [x] Physique de vol de base
- [x] Génération procédurale de terrain
- [x] Système de météo dynamique
- [x] Mission "Escape the Hell"
- [x] Mode "Free Ride"
- [x] UI du menu principal
- [x] UI in-game
- [x] Système audio (vent, pluie, tonnerre)
- [x] Changement de vue cockpit/externe

### Fonctionnalités partielles

- [ ] Nuages volumétriques (implémenté mais nécessite optimisation)
- [ ] Système d'aéroports (génération fonctionnelle, interactions limitées)
- [ ] Turbulences atmosphériques (modèle simplifié)



## Problèmes connus

| ID      | Description                                           | Sévérité | Status      |
| ------- | ----------------------------------------------------- | -------- | ----------- |
| BUG-001 | Performance réduite avec nuages volumétriques activés | Moyenne  | En cours    |
| BUG-002 | Turbulences parfois trop prononcées à basse altitude  | Faible   | À corriger  |
| BUG-003 | Génération de terrain peut causer des pics de latence | Moyenne  | À optimiser |

Voir [TESTING.md](./_7TESTING.md) pour la liste complète et les procédures de reproduction.



## Licence

**À compléter**: Informations de licence du projet

## Crédits


### Développement escape hell 
- **Auteur principale**: [Kokou]
- **Copilot**

### Bibliothèques tierces

- **Mouse Flight Controller** - Contrôles de vol
- **Unity Mesh Simplifier** (Whinarn) - Optimisation des meshes
- **Hydraulic Erosion** - Sebastian Lague (adapté)

### Assets

- **Sons**: [(https://pixabay.com/)]
- **Modèles 3D**: [unity asset Store, + TDs + Projet de base]

## Références

- [Documentation Unity](https://docs.unity3d.com/)
- [C# Programming Guide](https://learn.microsoft.com/en-us/dotnet/csharp/)
- [Real-Time Rendering Resources](http://www.realtimerendering.com/)
- [GPU Gems - Cloud Rendering](https://developer.nvidia.com/gpugems/gpugems2/part-ii-shading-lighting-and-shadows/chapter-16-accurate-atmospheric-scattering)


---

_Ce document fait partie de la documentation technique de Tiny Flight Simulator. Pour toute question, consulter les documents spécialisés ou contacter l'équipe de développement._
