---
title: Guide dev
---

# Guide Développeur - Tiny Flight Simulator

## Table des matières

1. [Environnement de développement](#environnement-de-développement)
2. [Structure du projet](#structure-du-projet)
3. [Standards de code](#standards-de-code)
4. [Workflow de développement](#workflow-de-développement)
5. [Debugging et profiling](#debugging-et-profiling)
6. [Extension du système](#extension-du-système)
7. [Build et déploiement](#build-et-déploiement)
8. [Bonnes pratiques](#bonnes-pratiques)

## Environnement de développement

### Prérequis

| Outil | Version | Usage |
|-------|---------|-------|
| **Unity Editor** | 2022.3+ LTS | Développement principal |
| **Visual Studio** | 2022 Community+ | IDE C# |
| **Git** | 2.30+ | Contrôle de version |
| **.NET SDK** | .NET Standard 2.1 | Runtime C# |


### Installation Unity

```powershell
# Installer Unity Hub
# Télécharger depuis https://unity.com/download

# Installer Unity 2022.3 LTS avec:
# - Windows Build Support
# - Visual Studio Community
# - Documentation (optionnel)
```

### Configuration Visual Studio

**Extensions recommandées**:
- Unity Tools for Visual Studio
- ReSharper (optionnel)
- Visual Studio IntelliCode

**Paramètres Unity**:
```csharp
// Edit > Preferences > External Tools
// External Script Editor: Visual Studio
// Generate .csproj files: Embedded packages, Local packages
```

### Configuration Git

**Fichier .gitignore**:
```gitignore
# Unity generated
[Ll]ibrary/
[Tt]emp/
[Oo]bj/
[Bb]uild/
[Bb]uilds/
[Ll]ogs/
[Uu]ser[Ss]ettings/

# Visual Studio
.vs/
*.csproj
*.unityproj
*.sln
*.suo
*.user

# Unity
*.pidb.meta
*.pdb.meta
*.mdb.meta

# OS
.DS_Store
Thumbs.db
```


## Structure du projet

### Organisation des dossiers

```
Assets/
├── _Scenes/                    # Scènes Unity
│   ├── MainMenu.unity
│   └── Flight Demo.unity
│
├── Scripts/                    # Code source C#
│   ├── Core/                  # Systèmes fondamentaux
│   │   └── README.md         # Documentation
│   │
│   ├── Aircraft/              # Systèmes avion
│   │   ├── Plane.cs
│   │   ├── AircraftColorApplier.cs
│   │   └── PlaneGroundStability.cs
│   │
│   ├── Weather/               # Système météo
│   │   ├── DynamicWeatherSystem.cs
│   │   └── AtmosphericTurbulence.cs
│   │
│   ├── Mission/               # Gestion missions
│   │   └── MissionManager.cs
│   │
│   ├── World/                 # Génération procédurale
│   │   ├── ProceduralWorldManager.cs
│   │   └── TerrainGenerator.cs
│   │
│   ├── UI/                    # Interface utilisateur
│   │   ├── MainMenuController.cs
│   │   └── GameMenuController.cs
│   │
│   ├── Camera/                # Systèmes caméra
│   │   └── CameraViewSwitcher.cs
│   │
│   ├── Rendering/             # Rendu avancé
│   │   └── VolumetricCloudsRenderer.cs
│   │
│   └── 3rd Party/             # Bibliothèques externes
│       ├── MouseFlight/
│       └── MeshSimplifier/
│
├── Shaders/                   # Shaders personnalisés
│   └── VolumetricClouds.shader
│
├── Materials/                 # Matériaux
├── Models/                    # Modèles 3D (.fbx, .obj)
├── Textures/                  # Textures (.png, .jpg)
├── Audio/                     # Fichiers audio (.wav, .ogg)
├── Resources/                 # Ressources chargées dynamiquement
├── Prefabs/                   # Préfabriqués Unity
└── Editor/                    # Scripts éditeur

Docs/                          # Documentation
├── README.md
├── ARCHITECTURE.md
├── USER_MANUAL.md
├── MISSION_SYSTEM.md
├── WEATHER_SYSTEM.md
├── RENDERING.md
├── DEVELOPER_GUIDE.md (ce fichier)
├── TESTING.md
└── images/                    # Images documentation
```

### Conventions de nommage

**Fichiers**:
- Scripts C#: PascalCase - `DynamicWeatherSystem.cs`
- Shaders: PascalCase - `VolumetricClouds.shader`
- Scènes: PascalCase - `Flight Demo.unity`
- Prefabs: PascalCase - `PlayerAirplane.prefab`

**Dossiers**:
- PascalCase pour catégories - `Scripts/`, `Weather/`
- Préfixe `_` pour dossiers prioritaires - `_Scenes/`

## Standards de code

### Conventions C#

**Classes**:
```csharp
/// <summary>
/// Description de la classe
/// </summary>
public class ExampleSystem : MonoBehaviour
{
    // Ordre des membres:
    // 1. Champs publics (Inspector)
    // 2. Champs privés
    // 3. Propriétés
    // 4. Méthodes Unity (Awake, Start, Update...)
    // 5. Méthodes publiques
    // 6. Méthodes privées
}
```

**Champs**:
```csharp
[Header("Section Name")]
[Tooltip("Description du champ")]
[Range(0f, 1f)]
public float publicField = 0.5f;

[SerializeField]
private float serializedPrivateField;

private float privateField;
```

**Méthodes**:
```csharp
/// <summary>
/// Description de la méthode
/// </summary>
/// <param name="parameter">Description du paramètre</param>
/// <returns>Description du retour</returns>
public ReturnType MethodName(ParamType parameter)
{
    // Code
}
```

**Coroutines**:
```csharp
private Coroutine exampleCoroutine;

void Start()
{
    exampleCoroutine = StartCoroutine(ExampleRoutine());
}

IEnumerator ExampleRoutine()
{
    yield return new WaitForSeconds(1f);
    // Code
}

void OnDestroy()
{
    if (exampleCoroutine != null)
    {
        StopCoroutine(exampleCoroutine);
    }
}
```

### Style de code

**Indentation**: 4 espaces (pas de tabulations)

**Accolades**: Style Allman
```csharp
if (condition)
{
    // Code
}
else
{
    // Code
}
```

**Espacement**:
```csharp
// Bon
float value = CalculateValue(param1, param2);

// Éviter
float value=CalculateValue(param1,param2);
```

**Longueur de ligne**: Maximum 120 caractères

**Commentaires**:
```csharp
// Commentaire court sur une ligne

/*
 * Commentaire multi-lignes
 * pour explications détaillées
 */

/// <summary>
/// Documentation XML pour IntelliSense
/// </summary>
```

### Gestion des erreurs

**Validation des paramètres**:
```csharp
public void SetValue(float value)
{
    if (value < 0f || value > 1f)
    {
        Debug.LogWarning($"Valeur invalide: {value}. Clamping à [0,1]");
        value = Mathf.Clamp01(value);
    }
    
    this.value = value;
}
```

**Vérification des références**:
```csharp
void Start()
{
    if (requiredComponent == null)
    {
        Debug.LogError("RequiredComponent non assigné!", this);
        enabled = false;
        return;
    }
}
```

**Try-Catch pour opérations risquées**:
```csharp
try
{
    // Opération risquée (I/O, parsing, etc.)
}
catch (System.Exception e)
{
    Debug.LogException(e, this);
}
```

## Workflow de développement

### Branches Git (si applicable)

```
main (ou master)        - Code stable, releases
├── develop             - Développement actif
│   ├── feature/xxx     - Nouvelles fonctionnalités
│   ├── bugfix/xxx      - Corrections de bugs
│   └── hotfix/xxx      - Corrections urgentes
```


### Processus de développement

**1. Planification**:
- Définir les objectifs
- Créer des tâches/issues
- Estimer la complexité

**2. Implémentation**:
- Créer une branche feature
- Développer avec commits réguliers
- Tester localement

**3. Révision**:
- Revue de code (si équipe)
- Tests fonctionnels
- Vérification performance

**4. Intégration**:
- Merge vers develop
- Tests d'intégration
- Résolution des conflits

**5. Release**:
- Merge develop vers main
- Build de release
- Tagging de version

### Commits

**Format des messages**:
```
[Type] Sujet court (50 caractères max)

Description détaillée si nécessaire.
Peut contenir plusieurs lignes.

Références: #issue-number
```

**Types**:
- `[Feature]` - Nouvelle fonctionnalité
- `[Fix]` - Correction de bug
- `[Refactor]` - Refactoring de code
- `[Docs]` - Documentation
- `[Perf]` - Amélioration performance
- `[Test]` - Ajout/modification tests
- `[Style]` - Formatage, whitespace

**Exemples**:
```
[Feature] Ajout système de nuages volumétriques

Implémentation Ray Marching pour rendu volumétrique.
Intégration avec DynamicWeatherSystem.

[Fix] Correction pluie ne suivant pas l'avion

Ajout de vérification Camera.main à chaque frame.
Repositionnement dynamique des particules.

[Perf] Optimisation du ray marching

Sortie anticipée si transmittance < 0.01.
Limitation verticale pour éviter calculs inutiles.
```

## Debugging et profiling

### Outils de debugging

**Console Unity**:
```csharp
Debug.Log("Message informatif");
Debug.LogWarning("Avertissement");
Debug.LogError("Erreur");
Debug.LogException(exception, this);

// Avec contexte
Debug.Log("Message", gameObject);
```

**Gizmos pour visualisation**:
```csharp
void OnDrawGizmos()
{
    Gizmos.color = Color.red;
    Gizmos.DrawWireSphere(transform.position, radius);
    
    Gizmos.color = Color.blue;
    Gizmos.DrawLine(start, end);
}

void OnDrawGizmosSelected()
{
    // Affiché seulement si l'objet est sélectionné
}
```

**Debug.DrawLine**:
```csharp
void Update()
{
    Debug.DrawRay(transform.position, transform.forward * 10f, Color.green);
}
```

### Unity Profiler

**Accès**: Window > Analysis > Profiler

**Sections importantes**:
- **CPU Usage** - Temps par script
- **GPU Usage** - Temps de rendu
- **Memory** - Allocation mémoire
- **Rendering** - Draw calls, batches

**Markers personnalisés**:
```csharp
using Unity.Profiling;

void Update()
{
    ProfilerMarker marker = new ProfilerMarker("MyFunction");
    
    marker.Begin();
    MyExpensiveFunction();
    marker.End();
}
```

### Frame Debugger

**Accès**: Window > Analysis > Frame Debugger

**Utilisation**:
1. Cliquer "Enable"
2. Naviguer dans les draw calls
3. Identifier les goulots d'étranglement

**Cas d'usage**:
- Identifier les shaders coûteux
- Vérifier le batching
- Analyser les post-effects

### Logs personnalisés

**Système de logging**:
```csharp
public static class GameLogger
{
    private static bool debugMode = true;
    
    public static void Log(string message, LogLevel level = LogLevel.Info)
    {
        if (!debugMode && level == LogLevel.Debug)
            return;
        
        string prefix = $"[{System.DateTime.Now:HH:mm:ss}] [{level}]";
        
        switch (level)
        {
            case LogLevel.Debug:
            case LogLevel.Info:
                Debug.Log($"{prefix} {message}");
                break;
            case LogLevel.Warning:
                Debug.LogWarning($"{prefix} {message}");
                break;
            case LogLevel.Error:
                Debug.LogError($"{prefix} {message}");
                break;
        }
    }
}

public enum LogLevel
{
    Debug,
    Info,
    Warning,
    Error
}
```

**Utilisation**:
```csharp
GameLogger.Log("Système initialisé", LogLevel.Info);
GameLogger.Log($"Valeur: {value}", LogLevel.Debug);
GameLogger.Log("Erreur critique", LogLevel.Error);
```

## Extension du système

### Ajouter un nouveau système

**1. Créer la classe**:
```csharp
using UnityEngine;

/// <summary>
/// Description du système
/// </summary>
public class NewSystem : MonoBehaviour
{
    [Header("Configuration")]
    [Tooltip("Description")]
    public float parameter = 1.0f;
    
    void Start()
    {
        Initialize();
    }
    
    void Initialize()
    {
        // Code d'initialisation
    }
    
    void Update()
    {
        // Code de mise à jour
    }
}
```

**2. Intégrer avec systèmes existants**:
```csharp
public class DynamicWeatherSystem : MonoBehaviour
{
    public NewSystem newSystem;  // Référence
    
    void UpdateWeather()
    {
        if (newSystem != null)
        {
            newSystem.DoSomething(weatherIntensity);
        }
    }
}
```

**3. Tester**:
- Tests unitaires (si applicable)
- Tests d'intégration
- Tests performance

**4. Documenter**:
- Commentaires XML
- Documentation utilisateur
- Entrée dans architecture

### Ajouter une nouvelle mission

**Voir**: [MISSION_SYSTEM.md](./_3MISSION_SYSTEM.md) - Section "Extension du système"

**Résumé**:
1. Définir paramètres dans `MissionManager.cs`
2. Ajouter détection dans `ApplyMissionSettings()`
3. Implémenter logique spécifique
4. Créer routine si nécessaire
5. Ajouter à l'UI de sélection

### Ajouter un nouveau shader

**1. Créer le fichier shader**:
```hlsl
Shader "Custom/NewShader"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        _Color ("Color", Color) = (1,1,1,1)
    }
    
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"
            
            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };
            
            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };
            
            sampler2D _MainTex;
            float4 _MainTex_ST;
            float4 _Color;
            
            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                return o;
            }
            
            float4 frag (v2f i) : SV_Target
            {
                float4 col = tex2D(_MainTex, i.uv) * _Color;
                return col;
            }
            ENDCG
        }
    }
}
```

**2. Créer le contrôleur C#**:
```csharp
public class NewShaderController : MonoBehaviour
{
    public Material shaderMaterial;
    
    void Update()
    {
        if (shaderMaterial != null)
        {
            shaderMaterial.SetFloat("_Parameter", value);
        }
    }
}
```

**3. Tester performance**:
- Unity Profiler > GPU
- Frame Debugger
- Différentes résolutions

## Build et déploiement

### Configuration de build

**File > Build Settings**:

**Platform**: Windows (x64)

**Scenes in Build**:
1. MainMenu
2. Flight Demo

**Player Settings** (importantes):
```
Company Name: [À compléter]
Product Name: tiny-flight-simulator-beta
Version: 0.9.0
Icon: [À assigner]

Resolution:
- Default Fullscreen Mode: Fullscreen Window
- Default Resolution: 1920x1080

Quality:
- Default Quality Level: High

Other Settings:
- Scripting Backend: IL2CPP
- API Compatibility Level: .NET Standard 2.1
```

### Processus de build

**1. Préparation**:
```csharp
// Vérifier les références
// Nettoyer les logs de debug
// Valider les scènes
```

**2. Build**:
```
File > Build Settings > Build
Sélectionner dossier de sortie: Build/
```

**3. Test du build**:
- Lancer l'exécutable
- Tester toutes les fonctionnalités
- Vérifier les performances
- Tester sur configuration minimale

**4. Packaging**:
```powershell
# Créer archive
Compress-Archive -Path "Build/*" -DestinationPath "tiny-flight-simulator-v0.9.0.zip"
```

**5. Distribution**:
- Upload sur plateforme de distribution
- Créer release notes
- Mettre à jour documentation

### Build automatique (optionnel)

**Script de build**:
```csharp
using UnityEditor;

public class BuildScript
{
    [MenuItem("Build/Build Windows x64")]
    public static void BuildWindows()
    {
        string[] scenes = new string[]
        {
            "Assets/_Scenes/MainMenu.unity",
            "Assets/_Scenes/Flight Demo.unity"
        };
        
        BuildPlayerOptions options = new BuildPlayerOptions();
        options.scenes = scenes;
        options.locationPathName = "Build/tiny-flight-simulator.exe";
        options.target = BuildTarget.StandaloneWindows64;
        options.options = BuildOptions.None;
        
        BuildPipeline.BuildPlayer(options);
    }
}
```

## Bonnes pratiques

### Performance

**1. Éviter Update() quand possible**:
```csharp
// Préférer les événements
public event Action OnValueChanged;

// Ou coroutines pour vérifications périodiques
IEnumerator CheckPeriodically()
{
    while (true)
    {
        yield return new WaitForSeconds(1f);
        PerformCheck();
    }
}
```

**2. Object Pooling**:
```csharp
public class ObjectPool<T> where T : Component
{
    private Queue<T> objects = new Queue<T>();
    private T prefab;
    private Transform parent;
    
    public T Get()
    {
        if (objects.Count > 0)
        {
            T obj = objects.Dequeue();
            obj.gameObject.SetActive(true);
            return obj;
        }
        return Object.Instantiate(prefab, parent);
    }
    
    public void Return(T obj)
    {
        obj.gameObject.SetActive(false);
        objects.Enqueue(obj);
    }
}
```

**3. Caching de références**:
```csharp
// Mauvais
void Update()
{
    Camera.main.DoSomething();  // FindObjectOfType à chaque frame
}

// Bon
private Camera mainCamera;

void Start()
{
    mainCamera = Camera.main;
}

void Update()
{
    mainCamera.DoSomething();
}
```

### Mémoire

**1. Éviter allocations dans Update()**:
```csharp
// Mauvais
void Update()
{
    var list = new List<int>();  // Allocation chaque frame
}

// Bon
private List<int> list = new List<int>();

void Update()
{
    list.Clear();  // Réutilisation
}
```

**2. Détruire les objets créés dynamiquement**:
```csharp
void OnDestroy()
{
    if (dynamicMaterial != null)
    {
        DestroyImmediate(dynamicMaterial);
    }
    
    StopAllCoroutines();
}
```

### Code propre

**1. Single Responsibility Principle**:
```csharp
// Chaque classe a une responsabilité unique
public class WeatherController { }  // Contrôle météo
public class WeatherRenderer { }    // Rendu météo
public class WeatherAudio { }       // Audio météo
```

**2. Dependency Injection**:
```csharp
// Plutôt que FindObjectOfType
public class System : MonoBehaviour
{
    [SerializeField] private Dependency dependency;
    
    // Ou via constructeur (avec Zenject, VContainer, etc.)
}
```

**3. Interfaces pour abstraction**:
```csharp
public interface IWeatherEffect
{
    void Apply(float intensity);
    void Stop();
}

public class RainEffect : IWeatherEffect { }
public class SnowEffect : IWeatherEffect { }
```

### Documentation

**1. Commenter le "pourquoi", pas le "quoi"**:
```csharp
// Mauvais
// Incrémente i
i++;

// Bon
// Passer au prochain élément car celui-ci est invalide
i++;
```

**2. Documenter les classes publiques**:
```csharp
/// <summary>
/// Système gérant la météo dynamique.
/// Contrôle la pluie, le vent, les éclairs et l'éclairage.
/// </summary>
public class DynamicWeatherSystem : MonoBehaviour
```

**3. Maintenir la documentation à jour**:
- Mettre à jour lors des changements
- Vérifier cohérence avec le code
- Ajouter exemples d'utilisation

## Ressources

### Documentation Unity

- [Unity Manual](https://docs.unity3d.com/Manual/index.html)
- [Unity Scripting Reference](https://docs.unity3d.com/ScriptReference/)
- [Unity Best Practices](https://unity.com/how-to/programming-unity)

### Documentation C#

- [C# Programming Guide](https://learn.microsoft.com/en-us/dotnet/csharp/)
- [.NET API Browser](https://learn.microsoft.com/en-us/dotnet/api/)

### Communauté

- [Unity Forum](https://forum.unity.com/)
- [Unity Answers](https://answers.unity.com/)
- [Stack Overflow - Unity](https://stackoverflow.com/questions/tagged/unity3d)

### Outils

- [Unity Hub](https://unity.com/download)
- [Visual Studio](https://visualstudio.microsoft.com/)
- [Git](https://git-scm.com/)
- [Beyond Compare](https://www.scootersoftware.com/) - Diff/Merge

## Glossaire développeur

| Terme | Définition |
|-------|------------|
| **GameObject** | Conteneur Unity pour composants |
| **MonoBehaviour** | Classe de base pour scripts Unity |
| **Coroutine** | Fonction pouvant s'exécuter sur plusieurs frames |
| **Prefab** | Template d'objet réutilisable |
| **ScriptableObject** | Asset de données |
| **Serialization** | Sauvegarde/chargement de données |
| **Inspector** | Éditeur de propriétés Unity |
| **Scene** | Niveau ou écran du jeu |
| **Build** | Compilation du projet |
| **Profiling** | Analyse de performance |

---

*Document mis à jour: Décembre 2025*
