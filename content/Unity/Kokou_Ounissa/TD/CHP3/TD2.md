---
title: TD2
---
![lab monfuji](images/perlinnoise.png)

# Génération Procédurale de Terrains

## 1. Définition

La génération procédurale consiste à créer du contenu (terrains, textures, objets, niveaux…) de manière automatique à partir d'algorithmes plutôt que de tout concevoir manuellement.

Dans le cas des terrains 3D, cela signifie que la forme du relief (montagnes, vallées, plateaux, etc.) est calculée selon des règles mathématiques ou pseudo-aléatoires.

## 2. Principe Général

L'idée est d'utiliser une fonction mathématique qui, pour chaque point (x, z) du terrain, génère une hauteur `y = f(x, z)`.

Ainsi, au lieu de stocker un grand nombre de données, on peut régénérer à l'infini un monde à partir :

- d'une **graine (seed)**, qui permet de reproduire la même carte
- d'une **fonction de bruit** (comme Perlin Noise ou Simplex Noise)
- et parfois de **paramètres d'environnement** (climat, biomes, altitude maximale…)

## 3. Algorithmes Courants

Les plus utilisés sont :

- **Bruit de Perlin** (Ken Perlin, 1983) : crée des variations douces et naturelles, parfait pour des reliefs réalistes
- **Bruit Simplex** : version optimisée et plus rapide du Perlin
- **Diamond-Square Algorithm** : approche récursive pour générer des hauteurs fractales
- **Voronoï** : utile pour générer des régions ou biomes (plaines, montagnes, déserts…)

## 4. Contribution à la Diversité et à l'Immersion

Les mondes générés procéduralement :

- sont **quasi infinis** (le joueur découvre toujours de nouvelles zones)
- offrent une **rejouabilité élevée** (chaque partie est unique)
- permettent de réduire le travail artistique manuel, tout en conservant une cohérence visuelle grâce aux règles de génération
- créent une sensation d'exploration authentique car le joueur ne suit pas un design prévisible

## Script

```csharp
using UnityEngine;

public class ProceduralTerrain : MonoBehaviour
{
    public int width = 200;       // largeur du terrain
    public int depth = 200;       // profondeur du terrain
    public int height = 20;       // hauteur maximale
    public float scale = 20f;     // échelle du bruit
    public Terrain terrain;       // terrain à modifier

    void Start()
    {
        terrain.terrainData = GenerateTerrain(terrain.terrainData);
    }

    TerrainData GenerateTerrain(TerrainData terrainData)
    {
        terrainData.heightmapResolution = width + 1;
        terrainData.size = new Vector3(width, height, depth);
        terrainData.SetHeights(0, 0, GenerateHeights());
        return terrainData;
    }

    float[,] GenerateHeights()
    {
        float[,] heights = new float[width, depth];
        for (int x = 0; x < width; x++)
        {
            for (int z = 0; z < depth; z++)
            {
                float xCoord = (float)x / width * scale;
                float zCoord = (float)z / depth * scale;
                heights[x, z] = Mathf.PerlinNoise(xCoord, zCoord);
            }
        }
        return heights;
    }
}


```