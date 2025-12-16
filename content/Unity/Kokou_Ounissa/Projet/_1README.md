---
title: 1 - Vue d'ensemble
---

# Tiny Flight Simulator - Documentation Technique

## Vue d'ensemble

Tiny Flight Simulator est un simulateur de vol développé sous Unity, offrant une expérience de vol immersive avec génération procédurale de terrain, système météorologique dynamique, et système de missions narratives.

### Informations du projet

| Propriété                        | Valeur                     |
| -------------------------------- | -------------------------- |
| **Nom du projet**                | Tiny Flight Simulator Beta |
| **Moteur**                       | Unity 2022.3+              |
| **Langage**                      | C# (.NET Standard 2.1)     |
| **Plateforme cible**             | Windows (x64)              |
| **Version**                      | Beta 0.9                   |
| **Date de dernière mise à jour** | Décembre 2025              |

### Objectifs du projet

Le projet vise à créer un simulateur de vol accessible combinant:

- Une physique aéronautique réaliste
- Une génération procédurale de monde ouvert
- Un système météorologique dynamique avec effets visuels avancés
- Un système de missions scénarisées
- Des graphismes optimisés avec techniques de rendu avancées

## Structure de la documentation

Cette documentation est organisée en plusieurs fichiers pour faciliter la navigation:

| Document                                   | Description                                                    |
| ------------------------------------------ | -------------------------------------------------------------- |
| [USER_MANUAL.md](./USER_MANUAL.md)         | Manuel utilisateur complet avec guide de prise en main         |
| [ARCHITECTURE.md](./ARCHITECTURE.md)       | Architecture technique du système                              |
| [MISSION_SYSTEM.md](./MISSION_SYSTEM.md)   | Documentation du système de missions                           |
| [WEATHER_SYSTEM.md](./WEATHER_SYSTEM.md)   | Système météorologique dynamique                               |
| [RENDERING.md](./RENDERING.md)             | Systèmes de rendu avancés (Ray Marching, nuages volumétriques) |
| [DEVELOPER_GUIDE.md](./DEVELOPER_GUIDE.md) | Guide pour les développeurs                                    |
| [TESTING.md](./TESTING.md)                 | Procédures de test et validation                               |

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

**À vérifier**: Intégration complète du système de nuages volumétriques

### Système de missions

Deux modes de jeu disponibles:

1. **Escape the Hell** - Mission narrative avec atmosphère horrifique
2. **Free Ride** - Mode libre sans contraintes

Voir [MISSION_SYSTEM.md](./MISSION_SYSTEM.md) pour plus de détails.

## Technologies utilisées

### Moteur et frameworks

- **Unity 2022.3 LTS** - Moteur de jeu principal
- **Unity UI** - Interface utilisateur
- **Unity Audio** - Système audio
- **Unity Particle System** - Effets visuels

### Packages Unity

| Package                              | Version | Utilisation              |
| ------------------------------------ | ------- | ------------------------ |
| com.unity.textmeshpro                | 3.0+    | Texte amélioré pour l'UI |
| com.unity.render-pipelines.universal | 14.0+   | Pipeline de rendu URP    |
| Unity Mesh Simplifier                | Custom  | Optimisation des meshes  |

**À compléter**: Liste complète des packages et versions exactes

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

Voir [ARCHITECTURE.md](./ARCHITECTURE.md) pour une description détaillée.

## Prérequis système

### Configuration minimale

| Composant           | Spécification                          |
| ------------------- | -------------------------------------- |
| **OS**              | Windows 10 64-bit                      |
| **Processeur**      | Intel Core i5-6600K / AMD Ryzen 5 1600 |
| **Mémoire**         | 8 GB RAM                               |
| **Carte graphique** | NVIDIA GTX 1060 / AMD RX 580           |
| **DirectX**         | Version 11                             |
| **Espace disque**   | 2 GB disponible                        |

### Configuration recommandée

| Composant           | Spécification                           |
| ------------------- | --------------------------------------- |
| **OS**              | Windows 11 64-bit                       |
| **Processeur**      | Intel Core i7-8700K / AMD Ryzen 7 2700X |
| **Mémoire**         | 16 GB RAM                               |
| **Carte graphique** | NVIDIA RTX 2060 / AMD RX 5700 XT        |
| **DirectX**         | Version 12                              |
| **Espace disque**   | 4 GB disponible                         |

**À vérifier**: Tests de performance sur configurations minimales

## Démarrage rapide

### Installation pour développeurs

```powershell
# Cloner le dépôt (si applicable)
git clone <repository-url> tiny-flight-simulator-beta
cd tiny-flight-simulator-beta

# Ouvrir le projet dans Unity Hub
# File > Open Project > Sélectionner le dossier
```

### Lancement du jeu

1. Ouvrir Unity
2. Charger la scène `MainMenu` dans `Assets/_Scenes/`
3. Cliquer sur Play
4. Sélectionner une mission et un avion
5. Cliquer sur "Start Game"

Voir [USER_MANUAL.md](./USER_MANUAL.md) pour les contrôles détaillés.

## Captures d'écran

### Menu principal

![Menu principal](images/main_menu.png)

### Vol en conditions normales

![Vol normal](images/normal_flight.png)

### Mission "Escape the Hell"

![Mission Escape the Hell](images/mission_escape_hell.png)

### Système météorologique

![Orage](images/weather_storm.png)

### Nuages volumétriques

![Nuages volumétriques](images/volumetric_clouds.png)

**Note**: Les images doivent être placées dans le dossier `Docs/images/`

## Vidéos de démonstration

Les vidéos suivantes illustrent les fonctionnalités principales:

| Vidéo                       | Description                         | Durée   |
| --------------------------- | ----------------------------------- | ------- |
| `demo_flight_controls.mp4`  | Contrôles et maniabilité            | ~2 min  |
| `demo_mission_escape.mp4`   | Mission "Escape the Hell" complète  | ~12 min |
| `demo_weather_system.mp4`   | Transitions météorologiques         | ~3 min  |
| `demo_procedural_world.mp4` | Génération de terrain en temps réel | ~2 min  |

**Note**: Placer les vidéos dans `Docs/videos/` et référencer avec le chemin relatif

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

**À compléter**: État précis de chaque fonctionnalité

### Fonctionnalités en développement

- [ ] Collision avec le terrain
- [ ] Système de dommages
- [ ] Missions additionnelles
- [ ] Multijoueur
- [ ] Replay de vol

## Problèmes connus

| ID      | Description                                           | Sévérité | Status      |
| ------- | ----------------------------------------------------- | -------- | ----------- |
| BUG-001 | Performance réduite avec nuages volumétriques activés | Moyenne  | En cours    |
| BUG-002 | Turbulences parfois trop prononcées à basse altitude  | Faible   | À corriger  |
| BUG-003 | Génération de terrain peut causer des pics de latence | Moyenne  | À optimiser |

Voir [TESTING.md](./TESTING.md) pour la liste complète et les procédures de reproduction.

## Support et contribution

### Rapporter un bug

Pour rapporter un bug, fournir:

- Description détaillée du problème
- Étapes de reproduction
- Configuration système
- Logs Unity (`%APPDATA%\..\LocalLow\<Company>\<Product>\Player.log`)
- Captures d'écran ou vidéos si applicable

### Contribuer au projet

Consulter [DEVELOPER_GUIDE.md](./DEVELOPER_GUIDE.md) pour:

- Standards de code
- Architecture des composants
- Processus de développement
- Guidelines de commit

## Licence

**À compléter**: Informations de licence du projet

## Crédits

### Développement

- **Équipe principale**: [À compléter]
- **Moteur**: Unity Technologies

### Bibliothèques tierces

- **Mouse Flight Controller** - Contrôles de vol
- **Unity Mesh Simplifier** (Whinarn) - Optimisation des meshes
- **Hydraulic Erosion** - Sebastian Lague (adapté)

### Assets

- **Sons**: [À compléter - sources des assets audio]
- **Modèles 3D**: [À compléter - sources des modèles]
- **Textures**: [À compléter - sources des textures]

## Références

- [Documentation Unity](https://docs.unity3d.com/)
- [C# Programming Guide](https://learn.microsoft.com/en-us/dotnet/csharp/)
- [Real-Time Rendering Resources](http://www.realtimerendering.com/)
- [GPU Gems - Cloud Rendering](https://developer.nvidia.com/gpugems/gpugems2/part-ii-shading-lighting-and-shadows/chapter-16-accurate-atmospheric-scattering)

## Contact

**À compléter**: Informations de contact du projet

---

_Ce document fait partie de la documentation technique de Tiny Flight Simulator. Pour toute question, consulter les documents spécialisés ou contacter l'équipe de développement._
