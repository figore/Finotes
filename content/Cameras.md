* # Import camera
    * export camera from blender with fbx (must have at least 1 key-frame - press I in the layout) baking animation
    * create a camera in UE
    * add a sequence
    * ![6408ecfba0f80848030e0b1b.png](6408ecfba0f80848030e0b1b_1731774818297_0.png)
    * right click sequencer
    * add actor to sequence -> camera from above
    * right click camera track -> import
    * ![6429326cf50da15b64c8eb0a.png](6429326cf50da15b64c8eb0a_1731774835525_0.png)
* # Camera shake
    * create blueprint class - camera shake base
    * double click - perlin noise
    * timing duration - 0 entire shot
    * compile and go to sequencer
	    * select camera and track+ -> camera shake
* # Camera rail
	* Add camera rail
	* parent whatever you want under the rail
	* select rail and animate the parameter Current position on rail