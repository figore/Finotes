# Cycles setup
- [[CyclesViewportSettings]]
- ![[Pasted image 20260217082052.png]]World tab 
	- Surface - Background
	- Color - Sky texture
# Cycles shadowmap
- ![[Pasted image 20260217131845.png]] Override material
	- diffuse white 0.8
	- roughness 1
# Heightmap
  * create a camera and reset the position
  * change output resolution to texture resolution (4k)
  * color depth to 16 bit
  * go under layers - Passes - Data - Z ✅
  * in camera settings change to orthographic
# Eevee baked preview
## Important 
- To preview the final output of a baked diffuse texture you need to connect it directly to surface input in the shader, bypassing the material itself. 
- Alternatively turn on eevee and switch to rendered mode (Z) now go to the top right of the viewport and disable Scene world
- ![[Pasted image 20260217172252.png]]
- Eevee settings
	- 128 samples
	- no shadow
	- no raytracing
- ![[Pasted image 20260217082052.png]]World tab 
	- Surface - Background
	- Color - baked hdr
		- Linear
	- ColorSpace - Linear Rec.709
- World shader in shader editor window
	- ![[Pasted image 20260217082329.png]]
	- select .hdr input image and ctrl+t
	- adjust rotation and scale