  * # How to render
    * ==rendering unhides all lights!!==
    * Enable movie render queue plugin
    * window - cinematics - movie render queue
    * Automotive: Temporal super-sampling enables rotational motion blur for car wheels, and high-resolution tiling allows large output images.
    * Use custom playback range and add frames
    * r.MotionBlurQuality 0
    * need to have a cut track for camera to render
    * hide actor in game will hide it from rendering
    * anti-alising - 32 is already a lot of samples
  * # Render settings
    * Unreal Engine makes real-time ray tracing possible through the use of denoising techniques that may rely on temporal history. When using high-resolution tiling or when disabling Temporal anti-aliasing, you may need to adjust the following console variables for better results:
    * ```
r.AmbientOcclusion.Denoiser.TemporalAccumulation 0
r.GlobalIllumination.Denoiser.TemporalAccumulation 0
r.Reflections.Denoiser.TemporalAccumulation 0
r.Shadow.Denoiser.TemporalAccumulation 0
```
    * Setting these variables to 0 will help the denoiser converge better without temporal history. You may also want to consider disabling the following Denoisers entirely if you have a high enough sample count (64+):
    * ```
r.AmbientOcclusion.Denoiser 0
r.DiffuseIndirect.Denoiser 0
r.Raytracing.SkyLight.Denoiser 0
r.Reflections.Denoiser 0
r.Shadow.Denoiser 0
```
    * To adjust any of the denoiser settings, you can add them to the Console Variables so they only apply during a render.
  * # Panorama
    * ## OLD
      * Enable Panoramic capture plugin
      * Content browser - show engine content - show plugin content
      * Content browser - panoramic capture content - assets - drag in  BP_capture
      * open the blueprint
      * Output bitdepth = 32
      * Monoscopic = 1
      * set outputdir to "/Panorama" (will be saved on drive C:/Panorama)
      * Compile, save
      * world settings - gamemode override = gamemode
When ran will make a panorama
if not:
      * press (tilda)
      * SP.panoramicscreenshot
      * Light shaft don't work
      * SP.ConcurrentCaptures = amount of captures rendered at the same time
    * ## 5.5
      * enable movie render queue additional render passes plugin
      * put the camera with 0 rotation and around 150cm on Z
      * add level sequence and put your camera with 1 frame
      * in movie render queue add panoramic render
  * # Cube map
    * right click and create texture - cube render target
    * cube plus upstairs and add scene capture cube
    * as a target you can select the previous texture
    * if you right click the texture now you can "create static texture"
  * # Pathtracing
    * **Enable:**
 Project settings
   Rendering
     * Support hardware raytracing
     * Pathtracing
     * Support Compute Skin Cache
   Windows
     * Targeted RHIs - Directx 12
  * # Parallax height
    * ![6546afb1a0f80857cc92a03a.png](6546afb1a0f80857cc92a03a_1731776211145_0.png)
**==heightmap channel has to be red!! not black!==**
    * ![64d69462a0f8084cb3794f17.png](64d69462a0f8084cb3794f17_1731776275047_0.png)
    * if there are weird artifacts, disable cast shadow from the plane