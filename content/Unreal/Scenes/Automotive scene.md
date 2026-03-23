  * ==Important note -only static lights work with reflection capture (rect, spot, etc)==
  * # Plugins:
    * sun position calculator - on
    * virtual production utilities - on
  * # Raytracing settings - might not work
    * raytracing - on
    * default RHI - DX12
  * # Settings:
    * autoexposure - off
    * reflection capture resolution - 2048+
    * clearcoat second normal - on
    * atmosphere - enable all
    * Gbuffer format - high precision normals
    * bloom - off
  * # Hit tilda key `
    * r.Reflections.Denoiser 0
    * r.RayTracing.Reflections.ReflectionCaptures 1
  * # Inside every mesh piece there is a parameter that might needs to be switched on:
    * Use high precision tangent basis - on
  * # Post processing volume
    * ## Raytracing reflections
      * max roughness - 0.6  (at witch point do we stop raytracing)
      * max bounces - 3
      * samples per pixel 1
    * ## Translucency
      * type raytracing
  * # HDRI settings:
    * Mip Gen Settings - no mipmaps
  * # Blurry reflections?
    * add a reflection capture!
  * # Lumen glass
    * ![](https://www.youtube.com/watch?v=GX9l7ug7YJY)