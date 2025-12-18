---
title: 7 - Tests et Validation
---

# Tests et Validation - Tiny Flight Simulator

## Table des matières

1. [Stratégie de test](#stratégie-de-test)
2. [Tests fonctionnels](#tests-fonctionnels)
3. [Tests de performance](#tests-de-performance)
4. [Tests de compatibilité](#tests-de-compatibilité)
5. [Tests de régression](#tests-de-régression)
6. [Procédures de validation](#procédures-de-validation)
7. [Rapports de bugs](#rapports-de-bugs)
8. [Checklist de release](#checklist-de-release)

## Stratégie de test

### Objectifs

- Garantir la stabilité du simulateur
- Valider les fonctionnalités critiques
- Identifier les problèmes de performance
- Assurer la compatibilité matérielle
- Prévenir les régressions

### Types de tests

| Type              | Description                     | Fréquence          |
| ----------------- | ------------------------------- | ------------------ |
| **Unitaires**     | Test de fonctions individuelles | Continu            |
| **Intégration**   | Test de systèmes combinés       | Par fonctionnalité |
| **Fonctionnels**  | Test de scénarios utilisateur   | Avant release      |
| **Performance**   | Mesure FPS, temps de chargement | Régulier           |
| **Compatibilité** | Test sur différentes configs    | Avant release      |
| **Régression**    | Vérification des bugs corrigés  | Après fix          |

### Environnements de test

**Configuration minimale**:

- OS: Windows 10 64-bit
- CPU: Intel Core i5-6600K
- RAM: 8 GB
- GPU: NVIDIA GTX 1060

**Configuration moyenne**:

- OS: Windows 10/11 64-bit
- CPU: Intel Core i7-8700K
- RAM: 16 GB
- GPU: NVIDIA RTX 2060

**Configuration haute**:

- OS: Windows 11 64-bit
- CPU: Intel Core i9-10900K
- RAM: 32 GB
- GPU: NVIDIA RTX 3070

## Tests fonctionnels

### Menu principal

#### TC-MENU-001: Démarrage du jeu

**Objectif**: Vérifier que le jeu démarre correctement

**Prérequis**: Aucun

**Étapes**:

1. Lancer tiny-flight-simulator.exe
2. Observer l'écran de chargement
3. Vérifier l'affichage du menu principal

**Résultat attendu**:

- Temps de chargement < 10 secondes
- Menu principal affiché correctement
- Aucune erreur console

#### TC-MENU-002: Sélection de mission

**Objectif**: Vérifier la sélection d'une mission

**Prérequis**: Menu principal affiché

**Étapes**:

1. Cliquer sur "Start Game"
2. Sélectionner "Escape the Hell"
3. Vérifier affichage du panneau de sélection d'avion

**Résultat attendu**:

- Panneau de sélection d'avion affiché
- Informations de la mission visibles
- Prévisualisation 3D fonctionnelle

#### TC-MENU-003: Sélection d'avion et couleur

**Objectif**: Vérifier la personnalisation de l'avion

**Prérequis**: Panneau de sélection d'avion affiché

**Étapes**:

1. Sélectionner un avion
2. Choisir une couleur
3. Observer la prévisualisation
4. Cliquer "Start Game"

**Résultat attendu**:

- Prévisualisation met à jour la couleur
- Transition vers scène de vol fluide
- Avion sélectionné spawne avec la couleur choisie

**Vidéo**: `test_aircraft_selection.mp4`

### Scène de vol

#### TC-FLIGHT-001: Chargement de la scène

**Objectif**: Vérifier le chargement correct de la scène de vol

**Prérequis**: Mission et avion sélectionnés

**Étapes**:

1. Observer l'écran de chargement
2. Attendre le chargement complet
3. Vérifier l'état initial

**Résultat attendu**:

- Temps de chargement < 15 secondes
- Avion spawne sur le sol
- Caméra positionnée correctement
- Météo appliquée selon mission

#### TC-FLIGHT-002: Contrôles de vol de base

**Objectif**: Vérifier les contrôles de l'avion

**Prérequis**: Scène de vol chargée

**Étapes**:

1. Augmenter puissance (W)
2. Observer accélération
3. Mouvoir souris pour contrôler assiette
4. Observer réponse de l'avion
5. Réduire puissance (S)

**Résultat attendu**:

- Augmentation de puissance visible et audible
- Avion répond aux mouvements de souris
- Assiette change selon input
- Réduction de puissance audible

**Vidéo**: `test_flight_controls.mp4`

#### TC-FLIGHT-003: Décollage

**Objectif**: Vérifier le décollage complet

**Prérequis**: Avion au sol

**Étapes**:

1. Puissance à 100% (maintenir W)
2. Attendre vitesse 30-35 km/h
3. Tirer doucement sur la souris
4. Observer le décollage
5. Stabiliser à 500m

**Résultat attendu**:

- Avion accélère progressivement
- Décollage autour de 30-35 km/h
- Montée stable sans décrochage
- Maintien d'altitude possible

**Vidéo**: `test_takeoff.mp4`

#### TC-FLIGHT-004: Vol en croisière

**Objectif**: Vérifier la stabilité en vol

**Prérequis**: Avion en vol à 500m

**Étapes**:

1. Stabiliser à 500m d'altitude
2. Puissance à 70%
3. Voler pendant 2 minutes
4. Observer la stabilité

**Résultat attendu**:

- Avion maintient l'altitude
- Pas de perte de contrôle
- Turbulences gérables
- FPS stable

#### TC-FLIGHT-005: Atterrissage

**Objectif**: Vérifier l'atterrissage

**Prérequis**: Avion en vol

**Étapes**:

1. Approcher l'aéroport
2. Réduire puissance progressivement
3. Descendre à 2-3° vers le sol
4. Vitesse 40-50 km/h
5. Toucher le sol

**Résultat attendu**:

- Descente contrôlée
- Atterrissage sur les roues
- Pas de rebond excessif
- Arrêt progressif

**Vidéo**: `test_landing.mp4`

#### TC-FLIGHT-006: Décrochage (Stall)

**Objectif**: Vérifier le comportement en décrochage

**Prérequis**: Avion en vol

**Étapes**:

1. Réduire puissance à 30%
2. Cabrer fortement (angle > 15°)
3. Observer perte de vitesse
4. Vérifier entrée en décrochage
5. Récupérer (pousser manche, puissance 100%)

**Résultat attendu**:

- Décrochage se produit à faible vitesse + angle élevé
- Perte de contrôle perceptible
- Récupération possible
- Son d'alarme (si implémenté)

#### TC-FLIGHT-007: Changement de vue caméra

**Objectif**: Vérifier le basculement de vue

**Prérequis**: Avion en vol

**Étapes**:

1. Appuyer sur V
2. Observer transition vers vue externe
3. Appuyer à nouveau sur V
4. Observer transition vers cockpit
5. Tester le free look (bouger souris en cockpit)

**Résultat attendu**:

- Transition instantanée
- Pas de double déclenchement
- Free look fonctionne en cockpit
- Vue externe suit l'avion correctement

**Statut**: Corrigé (bug double-clic résolu)

**Vidéo**: `test_camera_toggle.mp4`

### Système météorologique

#### TC-WEATHER-001: Ajustement météo en Free Ride

**Objectif**: Vérifier le contrôle de la météo

**Prérequis**: Mode Free Ride actif

**Étapes**:

1. Appuyer sur Échap (menu in-game)
2. Déplacer slider météo à 0.0
3. Observer changements
4. Déplacer slider à 1.0
5. Observer changements

**Résultat attendu**:

- Météo 0.0: Ciel clair, pas de pluie, vent nul
- Météo 1.0: Tempête, pluie intense, éclairs, vent fort
- Transitions fluides (pas de changement brusque)
- Audio s'adapte à l'intensité

**Vidéo**: `test_weather_transitions.mp4`

#### TC-WEATHER-002: Pluie suit l'avion

**Objectif**: Vérifier que la pluie suit la caméra

**Prérequis**: Météo > 0.5, avion en vol

**Étapes**:

1. Décollage
2. Voler à 1000m
3. Observer particules de pluie
4. Changer d'altitude
5. Vérifier suivi

**Résultat attendu**:

- Particules toujours visibles autour de l'avion
- Pas de "trou" dans la pluie
- Position mise à jour chaque frame

**Statut**: Corrigé (vérification Camera.main chaque frame)

#### TC-WEATHER-003: Éclairs et tonnerre

**Objectif**: Vérifier les effets d'orage

**Prérequis**: Météo > 0.7

**Étapes**:

1. Régler météo à 1.0
2. Observer pendant 1 minute
3. Compter les éclairs
4. Vérifier synchronisation son

**Résultat attendu**:

- Éclairs visuels (flash lumineux)
- Tonnerre synchronisé avec éclairs
- Intervalle 3-10 secondes
- Sons variés

**Vidéo**: `test_lightning_thunder.mp4`

#### TC-WEATHER-004: Brouillard dynamique

**Objectif**: Vérifier le brouillard

**Prérequis**: Brouillard activé (useFog = true)

**Étapes**:

1. Météo à 0.0 - Observer visibilité
2. Météo à 0.5 - Observer visibilité
3. Météo à 1.0 - Observer visibilité

**Résultat attendu**:

- Météo 0.0: Visibilité 2000m
- Météo 0.5: Visibilité ~1000m
- Météo 1.0: Visibilité 200m
- Transition fluide

### Mission "Escape the Hell"

#### TC-MISSION3-001: Conditions initiales

**Objectif**: Vérifier l'application des paramètres Mission 3

**Prérequis**: Mission 3 sélectionnée, scène chargée

**Étapes**:

1. Observer météo au spawn
2. Observer heure (minuit)
3. Tenter d'ajuster météo/heure
4. Vérifier verrouillage

**Résultat attendu**:

- Météo à 1.0 (tempête)
- Heure à 24h (minuit)
- Sliders météo/heure désactivés (grisés)
- Obscurité complète

#### TC-MISSION3-002: Effets d'horreur

**Objectif**: Vérifier les sons et images effrayants

**Prérequis**: Mission 3 active, en vol

**Étapes**:

1. Voler pendant 5 minutes
2. Compter apparitions sons (attendu: 5-10)
3. Compter apparitions images (attendu: 2-30)
4. Vérifier directions de défilement

**Résultat attendu**:

- Sons Halloween joués aléatoirement (30-60s)
- Image effrayante défile (10-120s)
- 4 directions possibles (haut, bas, gauche, droite)
- Volume audible mais pas écrasant

**Vidéo**: `test_mission3_horror_effects.mp4`

#### TC-MISSION3-003: Fin de mission après 10 minutes

**Objectif**: Vérifier la séquence de fin

**Prérequis**: Mission 3 active

**Étapes**:

1. Démarrer timer externe
2. Voler pendant 10 minutes
3. Observer changements à 10:00
4. Vérifier applaudissement
5. Vérifier déverrouillage UI

**Résultat attendu**:

- À 10:00 exactement:
  - Arrêt sons Halloween
  - Arrêt image effrayante
  - Météo passe à 0.0 (progressif)
  - Heure passe à 12h (progressif)
- À 10:01: Son d'applaudissement
- Sliders déverrouillés
- Contrôles utilisables

**Vidéo**: `test_mission3_complete.mp4` (durée: 12 minutes)

### UI In-Game

#### TC-UI-001: Menu in-game

**Objectif**: Vérifier le menu pause

**Prérequis**: En vol

**Étapes**:

1. Appuyer sur Échap
2. Vérifier affichage menu
3. Tester chaque bouton
4. Reprendre le jeu

**Résultat attendu**:

- Menu s'affiche au-dessus du jeu
- Jeu en pause
- Tous les boutons fonctionnels
- Resume reprend le jeu

#### TC-UI-002: HUD informations

**Objectif**: Vérifier l'affichage des informations

**Prérequis**: En vol

**Étapes**:

1. Observer HUD
2. Vérifier vitesse
3. Vérifier altitude
4. Vérifier puissance moteur

**Résultat attendu**:

- Informations visibles et lisibles
- Valeurs cohérentes
- Mise à jour en temps réel

**Statut**: À vérifier (liste exacte des éléments HUD)

### Génération procédurale

#### TC-WORLD-001: Génération de terrain

**Objectif**: Vérifier la génération du terrain

**Prérequis**: Scène de vol chargée

**Étapes**:

1. Observer terrain au spawn
2. Voler 5 km dans une direction
3. Observer nouveaux chunks
4. Vérifier absence de trous

**Résultat attendu**:

- Terrain généré autour du joueur
- Pas de trous dans le mesh
- Pas de pics de latence majeurs
- LOD fonctionne correctement

**À optimiser**: Pics de latence signalés

#### TC-WORLD-002: Aéroports

**Objectif**: Vérifier la génération d'aéroports

**Prérequis**: Scène de vol chargée

**Étapes**:

1. Localiser un aéroport
2. Atterrir dessus
3. Observer collision/interaction

**Résultat attendu**:

- Aéroports visibles
- Pistes plates
- Collision correcte

**Statut**: À vérifier (interactions limitées signalées)

## Tests de performance

### Métriques FPS

#### PERF-001: FPS en conditions normales

**Configuration**: Toutes

**Conditions**:

- Mode: Free Ride
- Météo: 0.0
- Nuages volumétriques: Désactivés
- Résolution: 1920x1080

**Procédure**:

1. Lancer le jeu
2. Voler pendant 5 minutes
3. Noter FPS min/moyen/max
4. Utiliser Unity Profiler si disponible

**Cible**:

- Config minimale: 30 FPS minimum
- Config moyenne: 45 FPS minimum
- Config haute: 60 FPS stable

**Résultats**:

| Config   | FPS Min  | FPS Moyen | FPS Max  |
| -------- | -------- | --------- | -------- |
| Minimale | À tester | À tester  | À tester |
| Moyenne  | À tester | À tester  | À tester |
| Haute    | À tester | À tester  | À tester |

#### PERF-002: FPS avec météo maximale

**Configuration**: Toutes

**Conditions**:

- Mode: Free Ride
- Météo: 1.0
- Nuages volumétriques: Désactivés
- Résolution: 1920x1080

**Procédure**: Identique PERF-001

**Cible**:

- Impact: < 10 FPS de perte vs conditions normales

**Résultats**: À tester

#### PERF-003: FPS avec nuages volumétriques

**Configuration**: Moyenne et haute uniquement

**Conditions**:

- Mode: Free Ride
- Météo: 0.5
- Nuages volumétriques: Activés (raySteps=50)
- Résolution: 1920x1080

**Procédure**: Identique PERF-001

**Cible**:

- Config moyenne: 30 FPS minimum
- Config haute: 45 FPS minimum

**Résultats**:

| Config  | FPS Min  | FPS Moyen | FPS Max  | Impact   |
| ------- | -------- | --------- | -------- | -------- |
| Moyenne | À tester | À tester  | À tester | À tester |
| Haute   | À tester | À tester  | À tester | À tester |

**Note**: Impact élevé signalé (30-45 FPS)

### Temps de chargement

#### PERF-004: Temps de chargement initial

**Procédure**:

1. Lancer l'exécutable
2. Chronométrer jusqu'à menu principal

**Cible**: < 10 secondes

**Résultats**: À tester

#### PERF-005: Temps de chargement scène de vol

**Procédure**:

1. Depuis menu, cliquer Start Game
2. Sélectionner mission/avion
3. Chronométrer jusqu'à contrôle disponible

**Cible**: < 15 secondes

**Résultats**: À tester

### Utilisation mémoire

#### PERF-006: Utilisation RAM

**Procédure**:

1. Lancer le jeu
2. Noter RAM initiale (Task Manager)
3. Jouer 15 minutes
4. Noter RAM maximale
5. Vérifier stabilité

**Cible**:

- < 4 GB utilisés
- Pas de fuite mémoire (croissance continue)

**Résultats**: Actuel: 2-3 GB (OK)

#### PERF-007: Utilisation VRAM

**Procédure**: Similaire PERF-006 (GPU-Z ou outils GPU)

**Cible**: < 2 GB

**Résultats**: Actuel: 1-1.5 GB (OK)

## Tests de compatibilité

### Résolutions d'écran

#### COMPAT-001: Différentes résolutions

**Résolutions à tester**:

- 1280x720 (720p)
- 1920x1080 (1080p)
- 2560x1440 (1440p)
- 3840x2160 (4K)

**Procédure**:

1. Changer résolution dans settings
2. Vérifier affichage UI
3. Vérifier performance
4. Vérifier lisibilité

**Résultat attendu**:

- UI s'adapte correctement
- Texte lisible
- Boutons accessibles
- Performance dégradée proportionnellement

### Modes d'affichage

#### COMPAT-002: Fullscreen vs Windowed

**Modes**:

- Fullscreen
- Fullscreen Windowed
- Windowed

**Procédure**: Tester chaque mode

**Résultat attendu**:

- Tous les modes fonctionnels
- Performance optimale en Fullscreen
- Alt+Tab fonctionne

### Périphériques d'entrée

#### COMPAT-003: Souris différentes

**Types**:

- Souris optique standard
- Souris gaming (DPI élevé)
- Trackpad laptop

**Procédure**: Tester contrôles avec chaque type

**Résultat attendu**:

- Contrôles fonctionnels avec tous
- Sensibilité acceptable
- Pas de comportement erratique

### Systèmes d'exploitation

#### COMPAT-004: Windows 10 vs Windows 11

**Procédure**:

1. Installer sur Windows 10
2. Tester toutes fonctionnalités
3. Répéter sur Windows 11

**Résultat attendu**:

- Fonctionnement identique
- Aucune erreur spécifique à l'OS

## Tests de régression

### Bugs corrigés à vérifier

#### BUG-001: Pluie ne suit plus l'avion

**Description**: Les particules de pluie restaient à la position initiale après décollage.

**Correction**: Ajout de vérification Camera.main à chaque frame.

**Test de régression**:

1. Régler météo à 1.0
2. Décoller
3. Voler à 1000m
4. Observer particules de pluie

**Résultat attendu**: Pluie toujours visible autour de l'avion.

**Statut**: Corrigé et vérifié

#### BUG-002: Double-clic bouton vue caméra

**Description**: Bouton Toggle View se déclenchait deux fois par clic.

**Correction**: Ajout de RemoveAllListeners() avant AddListener().

**Test de régression**:

1. En vol, ouvrir menu (Échap)
2. Cliquer une fois sur "Toggle View"
3. Vérifier changement de vue (une seule fois)

**Résultat attendu**: Vue change une seule fois par clic.

**Statut**: Corrigé et vérifié

#### BUG-003: Avion spawn à l'envers

**Description**: Parfois l'avion spawne avec rotation incorrecte.

**Correction**: Désactivation placement par WorldManager, position de scène Unity conservée.

**Test de régression**:

1. Relancer la scène 10 fois
2. Vérifier orientation de l'avion

**Résultat attendu**: Avion toujours orienté correctement (pas à l'envers).

**Statut**: Corrigé (rare, à surveiller)

#### BUG-004: Touche V ne fonctionne pas

**Description**: Après fix du double-clic, V key ne basculait plus la vue.

**Correction**: Suppression de la condition Cursor.visible.

**Test de régression**:

1. En vol, appuyer sur V
2. Vérifier changement de vue

**Résultat attendu**: Vue change à chaque pression de V.

**Statut**: Corrigé et vérifié

## Procédures de validation

### Validation pré-release

**Checklist complète**:

```
[ ] Tous les tests fonctionnels passent
[ ] Performance acceptable sur config minimale
[ ] Aucun bug critique
[ ] Documentation à jour
[ ] Build de release créé
[ ] Build testé sur 3 machines différentes
[ ] Logs de debug désactivés
[ ] Icône et métadonnées correctes
[ ] Taille du package raisonnable (< 1 GB)
[ ] Release notes rédigées
```

### Validation post-release

**Suivi**:

1. Collecter feedback utilisateurs
2. Surveiller rapports de bugs
3. Analyser métriques (si télémétrie)
4. Planifier hotfixes si nécessaire

## Rapports de bugs

### Template de rapport

```markdown
### Titre du bug

[Brève description en une phrase]

### Description

[Description détaillée du problème]

### Sévérité

- [ ] Critique (crash, bloquant)
- [ ] Majeure (fonctionnalité importante cassée)
- [ ] Mineure (inconvénient mais contournable)
- [ ] Cosmétique (visuel uniquement)

### Environnement

- OS: Windows 10/11
- CPU: [modèle]
- GPU: [modèle]
- RAM: [quantité]
- Version du jeu: [version]

### Étapes de reproduction

1. [Étape 1]
2. [Étape 2]
3. [...]

### Résultat attendu

[Ce qui devrait se passer]

### Résultat actuel

[Ce qui se passe réellement]

### Fichiers joints

- Player.log
- Screenshot.png (si applicable)
- Video.mp4 (si applicable)

### Informations supplémentaires

[Tout détail pertinent]
```

### Classification de bugs

**Critique**:

- Crash au démarrage
- Corruption de données
- Impossible de jouer

**Majeure**:

- Fonctionnalité principale cassée
- Performance très dégradée
- Contrôles ne répondent pas

**Mineure**:

- Bug visuel mineur
- Son manquant
- Traduction incorrecte

**Cosmétique**:

- Texture mal alignée
- Couleur incorrecte
- Typo dans UI

## Checklist de release

### Pré-build

```
[ ] Tous les tests passent
[ ] Pas de Debug.Log() dans le code
[ ] Version number incrémenté
[ ] Scene build list à jour
[ ] Player settings vérifiés
[ ] Icon assigné
[ ] Splash screen configuré
```

### Build

```
[ ] Build Windows x64 créé
[ ] Taille < 1 GB
[ ] Aucune erreur de build
[ ] Fichiers nécessaires présents
```

### Post-build

```
[ ] Exécutable lance sans erreur
[ ] Test rapide de toutes fonctionnalités
[ ] Test sur machine propre (sans Unity)
[ ] Package créé (.zip)
[ ] Checksum MD5 généré
```

### Distribution

```
[ ] Release notes rédigées
[ ] Changelog mis à jour
[ ] Documentation à jour
[ ] Archive uploadée
[ ] Annonce publiée
```

## Outils de test

### Unity Test Framework

**À implémenter**: Tests unitaires automatisés

**Exemple**:

```csharp
using NUnit.Framework;
using UnityEngine;

public class WeatherSystemTests
{
    [Test]
    public void SetWeatherIntensity_ClampsValue()
    {
        var weatherSystem = new GameObject().AddComponent<DynamicWeatherSystem>();

        weatherSystem.SetWeatherIntensity(1.5f);
        Assert.AreEqual(1.0f, weatherSystem.weatherIntensity);

        weatherSystem.SetWeatherIntensity(-0.5f);
        Assert.AreEqual(0.0f, weatherSystem.weatherIntensity);
    }
}
```

### Unity Performance Testing

**À implémenter**: Tests de performance automatisés

**Exemple**:

```csharp
using Unity.PerformanceTesting;

[Performance]
public class PerformanceTests
{
    [Test, Performance]
    public void MeasureFrameTime()
    {
        using (Measure.Frames().WarmupCount(10).MeasurementCount(100).Run())
        {
            // Code à mesurer
        }
    }
}
```

## Métriques de qualité

### Objectifs

| Métrique                 | Cible  | Actuel             |
| ------------------------ | ------ | ------------------ |
| Bugs critiques           | 0      | À mesurer          |
| Bugs majeurs             | < 5    | À mesurer          |
| FPS moyen (config min)   | > 30   | À mesurer          |
| FPS moyen (config haute) | > 60   | 50-60              |
| Temps de chargement      | < 15s  | À mesurer          |
| Utilisation RAM          | < 4 GB | 2-3 GB             |
| Couverture de tests      | > 50%  | 0% (à implémenter) |

### Suivi de la qualité

**Dashboard** (à créer):

- Nombre de bugs ouverts par sévérité
- Tendance FPS sur différentes configs
- Temps de résolution moyen des bugs
- Nombre de régressions

## Conclusion

Ce document établit les procédures de test et validation pour Tiny Flight Simulator. L'implémentation complète des tests automatisés et le suivi rigoureux des métriques permettront d'améliorer continuellement la qualité du simulateur.

**Prochaines étapes**:

1. Exécuter tous les tests fonctionnels
2. Mesurer la performance sur toutes configs
3. Implémenter tests automatisés Unity
4. Établir pipeline CI/CD (si applicable)
5. Créer dashboard de suivi qualité

---

_Document mis à jour: Décembre 2025_
