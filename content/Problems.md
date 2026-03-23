  * # General
    * **Bad size**
when player can't start cause of bad size it means it is probably intersecting with collision mesh
    * **Collision**
when you build an arch and can't pass through it, open the mesh settings
* change lit to colission
* find collision section on the right
* collision complexity - use complex collisions as simple
    * **Actors unloaded in level**
      * right click and pin
  * # PCG
    * **Lumen raytracing is being culled at big distances **
![lumen_shadows_01.gif](lumen_shadows_01_1728906528337_0.gif)
set r.RayTracing.Culling 0
    * **PCG trees look black at big distances**
![lumen_shadows_02.gif](lumen_shadows_02_1728906561310_0.gif)
disable raytraced shadow in light properties
      * assets://E%3A/Projects/Logseq/assets/lumen_shadows_02_1728906561310_0.gif
    * **PCG trees get culled at big distances**
![nanite_01.gif](nanite_01_1728906608914_0.gif)
enable preserve area under nanite settings for static mesh
    * **Slow PCG animation**
Unreal will recalculate a new collision mesh every frame, to disable 
it you need to set every static mesh collision type to NoCollision (keep
in mind that it updates every time you recalculate PCG)
If the performance is still low you can try disabling AO distance fields r.AOGlobalDistanceField 0
  * # Meshes
    * ## Meshes disappear based on camera angle
      * ![camera_angle_bug.gif](camera_angle_bug_1763641394519_0.gif)
      * go to actor or mesh setting and increases bounds scale
this happens because the pivot of the mesh is very far away and UE get confused if you are looking at it or not so this parameter will increase the bounding box which determines camera position (looking)
      * check LOD in static mesh and material
    * ## Delete the starter landscape
* “World Settings” and uncheck “Enable Streaming”
* then the button “Disable” under “Disable World Partition” will be available.
* Click on that and you are done.
![6526960ba0f8081c0f70bfba.png](6526960ba0f8081c0f70bfba_1729238535868_0.png)
    * ## Actors unloaded in level
* right click and pin
    * ## Broken geometry on import
![6514431c0e53ee2bb3cb0ab0.png](6514431c0e53ee2bb3cb0ab0_1729238733265_0.png)
* disable "remove degenerates" on import
  * # Materials
    * ## Wobbly materials
      * ![65141b930e53ee2bb3cb0a25.png](65141b930e53ee2bb3cb0a25_1729238653143_0.png)
      * enable high precision.... in SM editor
      * enable precise UVs
      * # have to click apply
    * ## HSL
  * # Rendering / lighting
    * ## No shadows from some meshes
      * In actor static mesh options "Evaluate World Position Offset in Ray Tracing"
    * ## Lumen reflection problem
      * ![6523cfbd0e53ee08505cd722.gif](6523cfbd0e53ee08505cd722_1729238796176_0.gif)
* Disable high quality translucency reflections in post processing volume
    * ## Whole scene is completely white
      * go to post processing settings and exposure, set min and max brightness to default value
    * ## Lumen not working
      * Lumen doesn't render when scalability is set to Medium or lower.
![64956984f50da165f150bff5.png](64956984f50da165f150bff5_1731774480111_0.png)
    * ## Disable baked lights
      * Window - ‘World Settings’ tab called “Force No Precomputted Lighting.” This deactivates Lightmass’ ability to produce light and shadowmaps, forcing the level to only use dynamic lighting.
    * ## Render output blurry
      * check focus plane
    * ## When rendering camera is in wrong position
      * add camera cuts
    * ## Triangle shadows
      * ![image.png](image_1761655663602_0.png)
      * Build - precompute static visibility
  * # Editor
    * ## Mouse too fast when playing the scene
      * * play the scene
* shift + 1 to release cursor
* press pause, in the outliner find BP_first_person_character and edit it
* add a multiply on x and y
![image.png](image_1730815953276_0.png)
  * # Errors
    * ## Render core and render graph definitions error on UE startup
delete DefaultEngine.ini
  * # Landscape
    * ## Weird shadows
      * ![image.png](image_1763645596259_0.png)
      * enable forward shading
    * ## Delete the starter landscape
      * * “World Settings” and uncheck “Enable Streaming”
* then the button “Disable” under “Disable World Partition” will be available.
* Click on that and you are done.
![6526960ba0f8081c0f70bfba.png](6526960ba0f8081c0f70bfba_1731355097180_0.png)
  * # Shadows
    * Shadows disappear at certain camera distance
      * ![shadow_distance.gif](shadow_distance_1756218274616_0.gif)
      * When you’re **not using Virtual Shadow Maps**, UE5 falls back to **Cascaded Shadow Maps (CSM)** for your directional light.
      * CSM uses a few cascades (usually 3–4) to cover a certain distance from the camera. Beyond that distance, **shadows stop being rendered**.
      * That distance is set by **Dynamic Shadow Distance**. If it’s, say, 10,000 units, then after 10m grass no longer receives per-object shadows → looks flat, no AO.
      * ## Set Dynamic Shadow Distance to 1-100
      * ---
      * ![contact_shadow_length.gif](contact_shadow_length_1763726483994_0.gif)
      * change contact shadow length in settings to 0.01