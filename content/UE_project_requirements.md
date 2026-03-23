# Folder structure
- ## General
	- Creating a folder named  `Meshes` ,  `Textures` , or  `Materials`
	- All asset names are named with their asset type in mind. 
- ## MaterialLibrary
	- If your project makes use of master materials, layered materials, or any form of reusable materials or textures that do not belong to any subset of assets, these assets should be located in `Content/Project/MaterialLibrary`
	- This way all 'global' materials have a place to live and are easily located.
	- The `MaterialLibrary` doesn't have to consist of purely materials. Shared utility textures, material functions, and other things of this nature should be stored here as well within folders that designate their intended purpose. For example, generic noise textures should be located in `MaterialLibrary/Utility`
* ## Always have a project folder at the top
	* in case you don't the materials will get overwritten during migration for example if you have Content/Maps and there you have level that is called Main - it will be overwritten during any migration of maps
    * Content
      * Car_environment_lake
        * Art
          * Nature
            * Trees
          * Building
            * Doors
            * Floors
        * Maps
        * MaterialLibrary
          * Metal
          * Paint
          * Utility
* # Naming
	* Prefix_BaseAssetName_Variant_Suffix
	* T_IY_CurbStone_01_diffuse (Texture_made by Igor Yurov_CurbStone_version01_diffuse)

	* SM - static mesh
	* M - material
	* MI - material instance
	* T - texture
	* BP - blueprint
	* **Always Use PascalCase** 
	* Level - ENV_LOCATION
# Cameras
- ![image.png](image_1758023333107_0.png)
# Static meshes
- all meshes need UVs
- TD has to be 1024 at 4k texture resolution (overlapping)
- for hero meshes (ones that have custom textures) no TD and UVs are whatever fits the asset
- Scale has to be proper for Unreal
- pivot has to be at 0.0.0 of the scene
- asset has to be in +y +x space (Z up)
- if the asset is modular it has to fit into 0-1 space or 0-2
- in case it overlaps it should then be snappable
- ![image.png](image_1755794428992_0.png)
# Levels
- **No Errors Or Warnings**
- All levels should load with zero errors or warnings. If a level loads with any errors or warnings, they should be fixed immediately to prevent cascading issues. You can run a map check on an open level in the editor by using the console command "map check".
# Content plugin
- every level is a content plugin
- to add a plugin go to Edit - plugins
- +add plugin - content plugin
## Source
- [github](https://github.com/Allar/ue5-style-guide)