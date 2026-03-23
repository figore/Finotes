  * ## ==**make sure that collision is set to NOcollision for all spawned meshes**==
  * # Creating PCG
    * right click in content browser - PCG (procedural content generation framework plugin)
    * drag into scene
    * double click the graph expand the nodes
    * landscape drag out - surface sampler (press D to debug) (if nothing is visible go to properties in volume and press generate)
    * scale down the samples and add density noise to the OUT (to randomize the scatter)
    * add denisty filter to lower the amount of points
    * add transform points - rotation max Z to 360, scale 0.7 - 1.2 (adds more randomization)
    * add static mesh spawner (add 4 array elements)
    * drag meshes into discriptor static mesh
    * adjust everythins with transform and filter nodes
    * create duplicate network and add a bounds modifier (so that trees don't intersect)
  * # Spline
    * Create blueprint class - Actor
    * Double click and add spline, in details add tag "IY_spline_01" to the actor
    * drag the blueprint into scene, hold alt to extend it
    * open your PCG
    * add get spline data - actor filter All world actors, selection tag "IY_spline_01"
    * ![64f6db730e53ee58b0ca803e.png](64f6db730e53ee58b0ca803e_1731775838154_0.png)
    * change settings in spline sampler to affect spawning
    * use projection with landscape for snapping
  * # Spline and snap to a mesh
    * ![6501a8760e53ee2f1efe724a.png](6501a8760e53ee2f1efe724a_1731775954812_0.png)
  * # Spline and bent curbs
    * source
      * ![](https://www.youtube.com/watch?v=hFZljPUWW8Q)
    * [BP_Splined_Mesh.uasset](BP_Splined_Mesh_1752497980194_0.uasset)
    * Howto
      * drag into viewport
      * add mesh
      * scroll down, there is a curve sampler to import
      * enable periodic placement and adjust distance
      * curve offset give problems, better use randomize transforms (use the button to update)
  * # Closed spline
    * ![6523fe2a0e53ee08505cd806.png](6523fe2a0e53ee08505cd806_1731776005772_0.png)
    * ![652401a80e53ee08505cd81d.png](652401a80e53ee08505cd81d_1731776013470_0.png)
  * # Mesh
    * create mesh sampler, add your original mesh there
    * get actor data and use tags to find the actor with the same mesh
    * copy points will basically find it in the scene
    * FORCE REGENERATE
    * ![image.png](image_1747827549008_0.png)
  * # Islands of meshes
    * ![blur2.gif](blur2_1758272917204_0.gif)
    * Spatial noise and density filter can give you islands of meshes
  * # Ray hit
    * ![650011090e53ee304b8af6cd.png](650011090e53ee304b8af6cd_1731775919793_0.png)
    * in world ray hit query try trace complex for static meshes and change channel according to what you want to project onto.
  * # Using textures
    * use get texture data node to import a texture
    * compression of the texture has to be vector displacement
    * no mipmaps
    * # **use high texel density or it will freeze > 500**
    * by default the points are flat, use projection node to fix
  * # Scale based on color
    * add execute blueprint node
    * search for scalebydensity blueprint