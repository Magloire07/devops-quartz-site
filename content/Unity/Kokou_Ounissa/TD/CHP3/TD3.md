---
title: TD3
---

![city proc gen](images/procGen.png)

# Génération Procédurale de Villes partie théorique : Principes de Base

## 1. Définition

La génération procédurale de villes consiste à créer automatiquement la disposition et les caractéristiques des éléments urbains (routes, bâtiments, parcs, zones industrielles, etc.) à partir d’algorithmes et de règles paramétrables, plutôt que de tout modéliser manuellement.
Cette approche permet d’obtenir des environnements variés et crédibles sans un travail manuel colossal.

## 2. Principes de Base

Une ville procédurale repose sur **trois couches principales** :

### Structure globale (layout)

→ Création du réseau routier et des zones (résidentielles, commerciales, industrielles).

- **Techniques** : L-systems, Voronoï, grilles fractales, agents simulant la croissance urbaine

### Placement des bâtiments

→ Selon la topographie, le type de zone, et des critères aléatoires contrôlés (densité, taille, hauteur).

### Détails visuels et vie urbaine

→ Ajout d'éléments comme les lampadaires, arbres, véhicules, PNJ, circulation, etc., pour renforcer la crédibilité et la vivacité du monde.

## 3. Techniques Courantes pour la Crédibilité

| Technique | Description | Effet visuel |visuel |
|-----------|-------------|--------------||
| **Bruit de Perlin** | Génère des variations douces pour définir densité ou type de bâtiments | Villes naturelles, moins uniformes |
| **L-Systems** | Systèmes grammaticaux simulant la croissance de routes | Réseaux réalistes de routes |
| **Subdivision récursive** | Division d'un grand bloc en lots plus petits | Quartiers bien structurés |
| **Grammaires procédurales (CGA)** | Règles décrivant comment les bâtiments sont extrudés ou décorés | Bâtiments variés et cohérents |
| **Agents basés sur la simulation** | Routes et structures placées selon des comportements simulés | Expansion organique, réaliste |

## 4. Crédibilité et Immersion

La **crédibilité** vient de la cohérence des règles (zones logiques, routes continues, bâtiments alignés) et de la variabilité contrôlée (pas de répétition stricte).

Une ville crédible donne au joueur le sentiment d'un monde vivant, logique et habité, ce qui renforce fortement l'immersion.

### Paramètres à ajuster

| Paramètre | Effet |
noiseScale Influence la taille des quartiers (plus petit → variations rapides, plus grand → zones homogènes)
densityThreshold Contrôle la densité des bâtiments (valeur haute = zones clairsemées)
seed Change totalement la disposition tout en restant reproductible
spacing Définit la distance entre bâtiments
type de prefab Crée de la variété architecturale

## 5. Avantages

### Efficacité et économie de ressources

- **Gain de temps** : les environnements urbains peuvent être générés automatiquement au lieu d'être modélisés à la main
- **Réduction du stockage** : seule la graine et quelques paramètres suffisent à régénérer une ville entière
- **Adaptabilité dynamique** : la carte peut se régénérer ou évoluer pendant le jeu (ex : villes différentes à chaque partie)

### Variabilité et immersion

- Le **bruit de Perlin** permet une variation douce et naturelle dans la densité, la hauteur ou le type de bâtiment
- Cela évite les motifs répétitifs (problème typique du pur hasard)
- Les transitions entre quartiers sont fluides, ce qui renforce la crédibilité visuelle et l'immersion du joueur

### Bénéfices dans le développement de jeu

| Aspect               | Bénéfice                                        |
| -------------------- | ----------------------------------------------- |
| Conception du monde  | Monde immense généré à la volée                 |
| Rejouabilité         | Chaque partie est unique                        |
| Performance          | Génération progressive (streaming procédural)   |
| Créativité du joueur | Possibilité d'interagir avec un monde dynamique |

---

# Le Réalisme dans la Génération Procédurale de Villes

## Partie Théorique : Réalisme et Cohérence des Environnements Urbains

### 1. Le Défi du Réalisme

La génération procédurale peut créer des villes vastes et variées, mais **le réalisme n'est pas automatique**.

Contrairement à une conception manuelle, un algorithme ne "comprend" pas intuitivement :

- les **contraintes humaines** (logique d'urbanisme, zones d'habitation, axes commerciaux)
- les **règles sociales** (densité variable selon les quartiers)
- ni la **cohérence visuelle** (alignement, matériaux, style architectural)

> Un moteur procédural doit donc intégrer des règles d'urbanisme simulées pour éviter des incohérences flagrantes.

### 2. Défis Majeurs à Surmonter

| Défi                    | Description                                                      | Solution potentielle                                                   |
| ----------------------- | ---------------------------------------------------------------- | ---------------------------------------------------------------------- |
| **Cohérence spatiale**  | Routes qui ne mènent nulle part, bâtiments qui se superposent    | Utiliser des graphes routiers contraints et une validation géométrique |
| **Variation contrôlée** | Trop d'aléatoire rend le monde incohérent                        | Utiliser du bruit cohérent (Perlin, Simplex) et des règles de zonage   |
| **Hiérarchie urbaine**  | Absence de structure (centre-ville, banlieue, zone industrielle) | Définir des niveaux de densité selon la distance au centre             |
| **Style architectural** | Mélange incohérent de styles                                     | Grouper les bâtiments par thème ou période                             |
| **Éléments humains**    | Villes vides ou statiques                                        | Ajouter des PNJ, circulation, bruit, végétation dynamique              |

### 3. Limite Fondamentale

Le réalisme procédural **n'égale pas encore la créativité humaine**, mais il peut s'en approcher grâce à :

- des **systèmes hybrides** (procédural + intervention artistique)
- des **règles de comportement** simulant la logique d'une vraie urbanisation
- et des **données réelles** (OpenStreetMap, topographie, densité réelle) intégrées comme base procédurale

### Exemple de Code : Zone Résidentielle

```csharp
if (densityValue < 0.4f) // zone résidentielle
{
    int blockSize = Random.Range(5, 10);
    for (int i = 0; i < blockSize; i++)
    {
        Vector3 offset = new Vector3(Random.Range(-5, 5), 0, Random.Range(-5, 5));
        Instantiate(housePrefab, basePosition + offset, Quaternion.identity);
    }
}
```

### Paramètres Clés

| Paramètre           | Rôle                                                        |
| ------------------- | ----------------------------------------------------------- |
| `densityValue`      | Contrôle le type de zone (résidentielle, commerciale, etc.) |
| `blockSize`         | Nombre de maisons par quartier                              |
| `spacing`           | Distance entre les habitations                              |
| `vegetationDensity` | Quantité de verdure autour                                  |
| `seed`              | Garantit la reproductibilité du même quartier               |

---

# Génération Procédurale de Villes et Optimisation de la Latence

## Partie Théorique : Exemples de Jeux et Défis de Latence

### 1. Exemples de Jeux Utilisant la Génération Procédurale de Villeslles

| Jeu                          | Caractéristiques                                                    | Type de génération                            |
| ---------------------------- | ------------------------------------------------------------------- | --------------------------------------------- |
| **Watch Dogs: Legion**       | Londres procédurale avec PNJ uniques                                | Génération hybride (préfabriqué + procédural) |
| **No Man's Sky**             | Planètes avec structures urbaines générées                          | Procédural complet                            |
| **Minecraft**                | Villages et structures créés dynamiquement                          | Procédural basé sur seed                      |
| **Cities: Skylines II**      | Gestion urbaine à grande échelle                                    | Génération semi-procédurale                   |
| **Cyberpunk 2077** (partiel) | Certains quartiers utilisent des procédés procéduraux pour le décor | Procédural partiel                            |

### 2. Défis de Latence

Les environnements urbains denses posent **trois grands défis de performance** :

| Défi                         | Description                            | Conséquence                   |
| ---------------------------- | -------------------------------------- | ----------------------------- |
| **Nombre élevé d'objets**    | Bâtiments, routes, véhicules, PNJ      | Temps de rendu élevé          |
| **Streaming de données**     | Le monde doit se charger dynamiquement | Risque de "freeze" ou popping |
| **IA et physique multiples** | Circulation, piétons, collisions       | Charge CPU importante         |

> Ces contraintes entraînent une latence accrue et des pertes de FPS si le moteur n'est pas optimisé.

---

## Partie Pratique : Stratégies d'Optimisation et Réduction de Latence

### Objectif

Permettre à un jeu procédural dense de maintenir des performances fluides.

### 1. Techniques de Génération Progressive

Générer la ville par zones ("chunks") uniquement quand le joueur s'en approche.

```csharp
if (Vector3.Distance(player.position, chunk.position) < loadDistance)
    chunk.Generate();
else
    chunk.Unload();
```

### 2. Level of Detail (LOD)

Utiliser plusieurs versions d'un même bâtiment :

- **LOD0** (détail complet)
- **LOD1** (simplifié)
- **LOD2** (cube distant)

Unity permet de gérer ça automatiquement avec le **LOD Group**.

### 3. Pooling et Instanciation Asynchrone

- **Réutiliser** des objets (bâtiments, voitures) au lieu de les recréer
- Utiliser des **coroutines Unity** pour répartir la charge de génération sur plusieurs frames :

```csharp
IEnumerator GenerateAsync()
{
    for (int i = 0; i < totalBuildings; i++)
    {
        Instantiate(buildingPrefab, positions[i], Quaternion.identity);
        if (i % 20 == 0) yield return null; // pause une frame
    }
}
```

### 4. Bruit Pré-calculé ou Compressé

Plutôt que de recalculer le bruit de Perlin à chaque frame, générer une carte de bruit en mémoire.

→ **Moins de calculs, plus de fluidité.**

### 5. Frustum Culling et Occlusion Culling

Ne pas rendre les objets en dehors du champ de vision de la caméra.

Unity le fait automatiquement, mais il peut être renforcé par des zones de visibilité personnalisées.

### 6. Multithreading et Jobs System

Utiliser le **Job System de Unity** ou **Burst Compiler** pour paralléliser la génération.

→ Exemple : calcul du bruit sur plusieurs cœurs CPU.

### Résumé des Stratégies

| Technique          | Impact principal                 |
| ------------------ | -------------------------------- |
| Chunks dynamiques  | Moins de mémoire utilisée        |
| LOD / Culling      | Moins de polygones rendus        |
| Coroutines / Async | Moins de blocage à la génération |
| Pooling d'objets   | Réduction du coût CPU            |
| Job System         | Calculs de bruit plus rapides    |

### Résultat Attenduu

> Une ville générée à la volée, fluide et cohérente, même avec une forte densité de bâtiments, sans latence perceptible.

---

## Synthèse Globale

| Aspect                     | Objectif                                     | Solution clé                                        |
| -------------------------- | -------------------------------------------- | --------------------------------------------------- |
| **Réalisme procédural**    | Créer des villes crédibles                   | Règles urbaines, zonage, densité cohérente          |
| **Quartiers résidentiels** | Donner une impression d'organisation humaine | Groupes de maisons, végétation, règles de placement |
| **Latence et performance** | Maintenir la fluidité                        | Génération par chunks, LOD, multithreading          |
| **Bruit de Perlin**        | Créer des variations naturelles et continues | Densité, altitude, types de bâtiments               |

---

# Exemples de Code

## Script 1 : Génération Procédurale de Ville

```csharp
using UnityEngine;

public class ProceduralCity : MonoBehaviour
{
    public GameObject[] buildingPrefabs; // différents modèles de bâtiments
    public int cityWidth = 50;
    public int cityDepth = 50;
    public float spacing = 10f;
    public float noiseScale = 0.1f;
    public float densityThreshold = 0.5f;
    public int seed = 42;

    void Start()
    {
        GenerateCity();
    }

    void GenerateCity()
    {
        Random.InitState(seed);

        for (int x = 0; x < cityWidth; x++)
        {
            for (int z = 0; z < cityDepth; z++)
            {
                float xCoord = (x + seed) * noiseScale;
                float zCoord = (z + seed) * noiseScale;
                float noiseValue = Mathf.PerlinNoise(xCoord, zCoord);

                // Densité contrôlée par le bruit
                if (noiseValue > densityThreshold)
                {
                    Vector3 position = new Vector3(x * spacing, 0, z * spacing);
                    GameObject prefab = buildingPrefabs[Random.Range(0, buildingPrefabs.Length)];
                    Instantiate(prefab, position, Quaternion.identity, transform);
                }
            }
        }
    }
}
```

## Script 2 : Génération de Terrain Urbain

```csharp
using UnityEngine;

public class UrbanTerrain : MonoBehaviour
{
    public int width = 100;
    public int depth = 100;
    public float heightScale = 3f;
    public float noiseScale = 0.05f;

    void Start()
    {
        MeshFilter meshFilter = GetComponent<MeshFilter>();
        meshFilter.mesh = GenerateMesh();
    }

    Mesh GenerateMesh()
    {
        Mesh mesh = new Mesh();

        Vector3[] vertices = new Vector3[(width + 1) * (depth + 1)];
        int[] triangles = new int[width * depth * 6];

        for (int z = 0, i = 0; z <= depth; z++)
        {
            for (int x = 0; x <= width; x++, i++)
            {
                float y = Mathf.PerlinNoise(x * noiseScale, z * noiseScale) * heightScale;
                vertices[i] = new Vector3(x, y, z);
            }
        }

        int vert = 0;
        int tris = 0;
        for (int z = 0; z < depth; z++)
        {
            for (int x = 0; x < width; x++)
            {
                triangles[tris + 0] = vert + 0;
                triangles[tris + 1] = vert + width + 1;
                triangles[tris + 2] = vert + 1;
                triangles[tris + 3] = vert + 1;
                triangles[tris + 4] = vert + width + 1;
                triangles[tris + 5] = vert + width + 2;
                vert++;
                tris += 6;
            }
            vert++;
        }

        mesh.vertices = vertices;
        mesh.triangles = triangles;
        mesh.RecalculateNormals();

        return mesh;
    }
}
```
