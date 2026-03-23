  * [[Software-Home]]
  * # Hotkeys
    * F2 and F3 to switch viewport
    * B - show baked maps
    * M - show material
    * C - switch channels
    * shift rmb - rotate ENV
    * alt click on mask shows it
  * # Exposure setup
	  
	  * ![[Pasted image 20260319074647.png]]
	  * ![[Pasted image 20260319074951.png]]
	  * white balance to 8000k for UE sun
	  * use panorama env hdr
	  * exposure 2.5
    
  * # Workflow [^1]
    * ## To simulate the same light as unreal sun take Bonidacio HDR and set exposure to 1
    * Drag and drop materials, right click on layer and add mask. If you drag smart mask onto the tiny mask icon it will be added.
    * Add **generator** as a very fancy mask (can combine baked masks)
    * If you apply**smart material**  you need to spend some time tweaking every single layer under it
    * To **paint stuff** add a paint layer, select material and paint
    * **Lathed metal** use Metal brushed smart material, disable all passes except normal. Change to different UV projection in material to orient stripes
    * **Decals**  - add a white fill layer, add black mask. Choose basic hard brush and switch library to alphas. Search for decals.
    * **Overall dust layer** - select rust smart material and add a dirt filter, now tweak stuff
  * # Clone
    * create a new layer and set it to passthrough
    * for every texture you want to clone you need to set this layer to passthrough
    * press V to sample an area
    * paint
  * # Problems
    * Symmetry
If the symmetry settings button is greyed out, enable symmetry, press f6 to switch to orthographic, save scene and reopen
## Projection
- **S + Left Mouse click** to rotate the stencil.
- **S + Left Mouse click + SHIFT** to snap/constrain to rotation of the stencil.
- **S + Right Mouse click** to Zoom/Unzoom the stencil.
- **S + Middle Mouse** click to translate the stencil.
# UDIMS
- ![[Pasted image 20251230143411.png]]
- tiny square on the right is the udim selector