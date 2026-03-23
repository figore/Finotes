# Flowify
## Slaps stitches onto surface
- create a strip with evenly spaced quads
- if they are not evenly spaced then use "space" in loop tools
- select strip and press the F icon ontop of the viewport - create mesh
- you will get a plane that represent a flat version of the strip
- position this plane under the stitch array
- select stitch array and in the f icon seleft flowify
- mouse over the new plane and select a corner
- mouse over the original strip and select a corner there
- done
## Tips
- transforms dont matter
- amount of polys dont matter
- can use a random plane for source grid
## Problems
- it will squeeze your stitch mesh into the strip, it wont cut it off
- sometimes the new plane will be messed up with unconnected vertices
	- just select the 4 boundary verts and dissolve the rest