---
title: TD4
---

# Effets de Post-Traitement

## 1. Compréhension des Fondamentaux

Les effets de post-traitement (post-processing effects) sont des filtres appliqués après le rendu principal de la scène 3D. Leur rôle est d'améliorer le rendu visuel en ajoutant des effets cinématographiques tels que le bloom, la profondeur de champ, le flou de mouvement, ou encore la correction colorimétrique.

Ces effets contribuent à :

- **Renforcer l'immersion du joueur** : un léger flou de profondeur ou une teinte chaude dans une zone ensoleillée rend le monde plus crédible
- **Créer une identité visuelle** : par exemple, un jeu d'horreur utilisera une désaturation et un grain pour une ambiance oppressante
- **Guider la perception** : la lumière et les contrastes peuvent attirer naturellement le regard vers des éléments importants

En somme, le post-traitement est un outil artistique puissant qui transforme une scène "brute" en une expérience visuellement cohérente et émotionnellement engageante.

## 2. Gestion des Ressources

Les effets de post-traitement peuvent être coûteux en calculs GPU, surtout sur les plateformes mobiles.

Voici les bonnes pratiques d'optimisation :

- **Limiter le nombre d'effets actifs simultanément** : éviter d'empiler plusieurs effets lourds (bloom + depth of field + motion blur)
- **Utiliser des profils de qualité adaptés** : proposer plusieurs niveaux de qualité (faible, moyen, élevé) selon le périphérique
- **Préférer les effets "Screen-Space" simples** plutôt que les traitements complexes par pixel
- **Utiliser le LOD (Level of Detail)** pour réduire la précision des effets sur les appareils moins puissants
- **Combiner les effets** dans un même pipeline via le Post-Processing Stack v2 de Unity, plus performant qu'un empilement d'effets séparés
- **Profiler régulièrement** avec l'outil Unity Profiler ou Frame Debugger pour détecter les goulots d'étranglement

3. Application pratique spécifique : Effet de flou de mouvement (Motion Blur)
   🧩 Étapes d’installation dans Unity :

Installer le Post-Processing Package :

Ouvrir Window → Package Manager → Post Processing.

Installer le package.

Configurer la caméra :

Ajouter un composant Post-process Layer à la caméra.

Définir le Layer (par exemple "PostProcessing").

Créer un profil de post-traitement :

Créer un Post-process Volume dans la scène (GameObject → Volume → Global Volume).

Associer un Post-process Profile.

Activer l’effet Motion Blur :

Dans le profil, cliquer sur Add Effect → Unity → Motion Blur.

Activer l’effet et ajuster les paramètres :

Shutter Angle : intensité du flou (ex : 180° pour un effet réaliste).

Sample Count : nombre d’échantillons pour lisser le flou (attention au coût GPU).

Tester en jeu :

Le flou s’appliquera aux objets ou à la caméra selon leur mouvement.

🎮 Utilité :

Améliore la sensation de vitesse (course, combat rapide).

Rend les transitions plus fluides entre les frames.

Donne un rendu cinématique inspiré du cinéma.

## 4. Intégration Avancée : Système Adaptatif Dynamique

Pour un effet plus réactif et immersif, on peut adapter les effets de post-traitement selon les conditions environnementales.

### Exemple

Lorsqu’un personnage entre dans une zone sombre, la scène devient plus froide, le contraste augmente et le grain s’ajoute pour simuler une ambiance oppressante.

### Méthode

- Créer plusieurs Post-process Volume avec des profils différents (ex : jour, nuit, intérieur sombre)
- Définir un blend distance pour que la transition soit douce
- Placer un Collider trigger dans la zone sombre
- Dans un script C# :

```csharp
using UnityEngine;
using UnityEngine.Rendering;
using UnityEngine.Rendering.Universal;

public class ZonePostProcess : MonoBehaviour
{
    public Volume darkZoneVolume;

    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Player"))
            darkZoneVolume.weight = 1f; // Active le profil sombre
    }

    void OnTriggerExit(Collider other)
    {
        if (other.CompareTag("Player"))
            darkZoneVolume.weight = 0f; // Retour à la normale
    }
}
```

Cela permet de changer dynamiquement l'ambiance du jeu selon le contexte.

## 5. Nouveautés et Tendances

Un effet particulièrement prometteur est le **Ray-Traced Global Illumination (RTGI)** ou l'éclairage global en temps réel.

### Pourquoi c'est intéressant

- Il simule la réflexion de la lumière indirecte de manière réaliste
- Permet des transitions naturelles entre les zones éclairées et ombrées
- Crée une ambiance lumineuse crédible sans devoir placer des centaines de sources artificielles

### Faisabilité technique

- Déjà partiellement disponible via Unity HDRP avec Ray Tracing (sur cartes RTX)
- De plus en plus optimisé pour les plateformes grand public
- Peut être combiné avec des techniques hybrides (Screen Space Global Illumination) pour les appareils sans ray tracing matériel

### Impact

Ce type d'effet pourrait devenir la norme dans les années à venir, offrant des mondes bien plus dynamiques, réalistes et vivants, sans dépendre de l'éclairage précalculé.
