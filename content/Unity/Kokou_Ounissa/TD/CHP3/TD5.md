---
title: TD5
---

![skydome1](images/skydome1.png)
![skydome2](images/skydome2.png)

# Skydome Using gpt

## Méthode 1 : Utiliser un Skybox Material

To use your skybox material:

**Go to:**
- Window → Rendering → Lighting → Environment

**Then set:**
- Skybox Material → (select one of your AllSky skybox materials)

Your sky changes immediately ✨

## Méthode 2 : Ajouter le Skydome en tant qu'Objet de Scène

But if you want to use a Skydome instead:

Then you need a Skydome mesh prefab (a sphere), and you must apply your material to that mesh.

**How to do that:**

**Create a big sphere:**
- GameObject → 3D Object → Sphere

**Rename it:**
- SkyDome

**Scale it very large:**
- e.g., Scale = 500, 500, 500

**Invert its normals (this is important!):**
- Otherwise you see the sphere from the outside, not from the inside

**Simplest method:**
- Select the sphere
- In Inspector, set Scale to -500 on X: Scale = (-500, 500, 500)

**Apply your skybox material to the sphere's Mesh Renderer:**
- Drag your .mat onto the sphere