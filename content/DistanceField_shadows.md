  * Distance Field Shadows (UE)
    * **Purpose** : Alternative dynamic shadowing system. Works at large distances without cascades.
    * **How they work** :
      * Uses mesh distance fields (voxelized 3D representations of meshes) to calculate shadows.
      * Instead of projecting from the light with shadow maps, it traces through the distance fields.
      * Especially good for large-scale, soft shadows far from the camera.
    * **Strengths** :
      * Shadows visible at very far distances (no need for cascades).
      * Produces soft, natural shadow penumbras.
      * Efficient for distant trees, terrain, foliage, skylight occlusion.
    * **Weaknesses** :
      * Needs **Generate Mesh Distance Fields** enabled in Project Settings.
      * Costs extra memory (distance field textures per mesh).
      * Close shadows are usually less detailed/sharp than CSM.
    * **Typical use** :
      * Use CSM for near, crisp detail.
      * Use Distance Field Shadows for far distance coverage (mountains, forests).
      * Common in large outdoor worlds.