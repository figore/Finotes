# General
- O - Occlusion (RED)
- R - Roughness (GREEN)
- Dp - Displacement (BLUE)
* texture sample - texture input
* you can adjust brightness of textures in their settings
* brightness/contrast is done with 3pointlevels
* invert node - oneminus
* uv scale
* ![6432f0e6f50da15f8501e4f9.png](6432f0e6f50da15f8501e4f9_1729193798214_0.png)
# Material instancing
- ![image.png](image_1734787905973_0.png)
- A generic shader for UE5 to have material instances
- right click a node and convert to parameter (color, normal, roughness)
- in details of the sample it has to say Sampler type: diffuse - color, roughness - masks, Normal - normal
- now if you create a material instance and select you material as parent you will have access to exposed parameters
# Brightness saturation contrast
- Material function call node (comes from fab/material functions)
- ![image.png](image_1754038967559_0.png)
## Blend screen and others
- ![651060e1a0f80827474cd8bd.png](651060e1a0f80827474cd8bd_1729193866875_0.png)
## Replace material
- ![642ae9f3f50da11c32f676ef.png](642ae9f3f50da11c32f676ef_1729193891607_0.png)
* find material with magnifying glass
* right click - asset actions select all actors
## Normals
- flip normal
- ![64343e5cf50da15f8501e830.png](64343e5cf50da15f8501e830_1729194000916_0.png)
- normal scale
- ![6432efa1f50da15f8501e4e5.png](6432efa1f50da15f8501e4e5_1729194037818_0.png)
## Displacement
- ![64e4b8490e53ee179bfad940.png](64e4b8490e53ee179bfad940_1729237237072_0.png)
## Overlay
- ![6505ed85a0f8084cb948b3a0.png](6505ed85a0f8084cb948b3a0_1729237253938_0.png)
## U and V parameters
- ![65199dfaa0f80819b205a4a8.png](65199dfaa0f80819b205a4a8_1729237304952_0.png)
## Material layers
- create a material function
- add make material attributes
- create master material and scroll down on the left
- check use material attributes
- use matlayerblendsimple for blending things
- drag in the material functions
- material functions for landscape
- ![6533e933f3fadc700a6ece0a.png](6533e933f3fadc700a6ece0a_1729237396301_0.png)
## Sprite (simple)
- or imposter is set up in the material
- ![image.png](image_1748633635126_0.png)
- control offsets with folliage
# Tiling
- [M_IY_random_tiling.uasset](M_IY_random_tiling_1745167343646_0.uasset)
# Substance masks
- ![ioannis-stamatelis-mentorshipterm-texturing-rgbmasks-ioannisstamatelis.jpg](ioannis-stamatelis-mentorshipterm-texturing-rgbmasks-ioannisstamatelis_1742413788295_0.jpg)
- ![ioannis-stamatelis-mentorshipterm-assets-rbgmasks-ioannisstamatelis.jpg](ioannis-stamatelis-mentorshipterm-assets-rbgmasks-ioannisstamatelis_1742413873301_0.jpg)
-  https://www.artstation.com/artwork/3E8Oag
# Substance to UE
- ORM
- disable srgb
- compression settings to masks
- sampler type to mask
# Vertex painting
## Basics
-  Just paint some color and then it will take all the vertex paint data and convert it to values
- [image.png](image_1742124014923_0.png)
## Noobsus
- Lerp is blending between two colors based on the red channel of vertex paint
- ![image.png](image_1742124088066_0.png)
## Easy
- Plug in the height map into lerp and vertex color is going to paint only on high points
- Contrast can be adjusted for sharper painting
- ![image.png](image_1742124270834_0.png)
## Medium
- so you make 2 materials and then paint between them
- click on the material node and enable material attributes on the left
- add make material attributes node
- instead of lerp you need blendmaterials attributes
- ![image.png](image_1742125611515_0.png)
- instead of transition you can put vertex color and it will switch between 2 materials
## Hard
- the edge between 2 materials is pretty simple, you can add a function to make it random
- create a material function
- noise texture is called macro variation
- ![image.png](image_1742126805273_0.png)
- that's how to connect it to the previous thing
- ![image.png](image_1742126878334_0.png)
## Nanite
- Source
      * ![](https://www.youtube.com/watch?v=OPHnxGb6KHA)
# Errors
- Opacity and virtual texture - disable virtual texture streaming in texture properties, switch to color sampler type