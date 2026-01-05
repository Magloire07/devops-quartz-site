Avant post processing : 

![image.png](attachment:a660e10c-83c3-4933-a9b0-3173ef0562a8:image.png)

Après ajout du post processing 

![image.png](attachment:39a7f128-af22-4236-a3f2-004d0e4faf29:image.png)

Ajustements réalisés:

![image.png](attachment:95520f33-daf2-4e12-aa9a-6cb1c43a4003:image.png)

---

**Réponses aux questions** 

**1 - Compréhension des fondamentaux :**

****Décrivez le rôle et l'importance des effets de post-traitement dans la création d'expériences
visuelles immersives dans Unity. Comment ces effets peuvent-ils influencer la perception du
joueur ?

Les effets de post-traitement améliorent la qualité visuelle d’une scène après son rendu par la caméra. Ils améliorent le réalisme du jeu et renforcent l’ambiance du jeu. 

Par exemple un filtre de couleur chaude rend l’atmosphère apaisante. Cela renforce donc l’immersion du jouer et l’émotion ressentie. 

**2 - Gestion des ressources :**

Les effets de post-traitement peuvent avoir un impact significatif sur les performances.
Quelles sont les bonnes pratiques pour optimiser l'utilisation des effets de post-traitement
dans un projet Unity, en particulier pour les jeux conçus pour les plateformes mobiles
récentes ?

Pour limiter l’impact des effets de post-processing sur les performances : Il faut déjà n’activer que les effets qui sont réellement pertinent pour notre scène. Également, on peut paramétrer certains effetsen “Low” pour diminuer leur précision. Et enfin on peut utiliser un Post-Process volume global au lieu de plusieurs locaux. 

**3 - Application pratique spécifique :**

Expliquez comment configurer un effet de flou de mouvement (Motion Blur) dans Unity, en
détaillant les étapes nécessaires et comment cet effet peut améliorer le réalisme d'une scène en mouvement rapide. 

Avec cet effet de flou pendant le mouvement, on a l’impression que le mouvement est plus rapide. Cet effet rend la scène plus cinématique 

**4 - Intégration avancée :**
Comment intégreriez-vous un système d'effets de post-traitement qui s'adapte
dynamiquement aux conditions environnementales du jeu (comme le temps ou l'éclairage)
dans Unity ? Fournissez un exemple spécifique, comme un changement d'ambiance lorsqu'un
personnage entre dans une zone sombre (exemple existant dans le cours théorique).

Pour le passage d’un personnage de l’extérieur vers une zone sombre, on peut créer différents profils de post-processing et les activer selon là zone où il se trouve à l’aide d’un script.

```csharp
if (InDarkZone)
    volume.profile = darkProfile;
else
    volume.profile = dayProfile;
```

Et pour les différents profils, on pourra ajuster les effets tels que la Temperature, l’Ambiant Occlusion etc

**5 - Nouveautés et tendances :**

Avec l'évolution constante de la technologie graphique, de nouveaux effets de post-
traitement sont régulièrement développés. Quel nouvel effet considérez-vous comme

particulièrement prometteur ou intéressant pour l'avenir des jeux développés avec Unity ?
Justifiez votre choix en termes d'impact visuel et de faisabilité technique.

L’effet que je considère particulièrement intéressante est l’effet vignette qui assombrit légèrement les bords de l’écran tout en gardant le centre plus lumineux. Il dirige naturellement le regard du joueur vers le centre.

Utilisé avec subtilité, il peut accentuer la tension dans une enquête ou le réalisme d’un moment dramatique, tout en restant léger à calculer et compatible avec toutes les plateformes.