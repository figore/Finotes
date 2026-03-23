# General
- use multires modifer with lower value for viewport
# Hardsurface workflow

- Scrape brush settings on the right:
	- click double sheet icon
	- rename brush preset
	- scroll to advanced
	- enable normals and plane
	- right click  plane offset depth of bevel
- Add bevel modifier on top
- Flatten brush
	- Scrape brush normal radius .1 to .3
	- plane trim = 0.2
	- Curves sharp  
	- select pinch brush
	- press E  c
	- ctrl + right click and drag for first handle
	- ctrl + right click and drag for end handle
	- press enter to apply, press again for more
	- press a to select the curve and x to delete
	- scrape brush to flatten the areas close to the curve
# Alphas
- N menu go to tool tab
- texture and click the texture button ![image.png](image_1732449593605_0.png)
- open BW alpha
- change mapping to View plane to remove tiling
- Stroke  anchored
- Falloff  smooth
- drag on the surface
- direction defines if it's gonna dig or build
# Sculpting tileable textures
- Create plane
- add 6 simple subdivisions (view), apply
- add multi resolution modifier and subdivide 5 times
- shade smooth
- in sculpting tab there is a symmetry section under tool tab on the right
      ![image.png](image_1734864374582_0.png)