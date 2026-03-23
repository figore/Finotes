  * # Pre-requisites
    * plugins - volumetrics
    * content viewer - settings - show engine content and show plugin content
  * # Start here
    * Empty level
    * Add volumetric cloud
      * assign new material from content browser - volumetrics content  - content - sky - materials - MI_VolumetricCloud_02_Profiles_PaintClouds_Morning
      * double click the material
        * bias = -0.2
        * speed  = 0.01
      * layer bottom altitude = -6 (in km, 0 is player level)
      * layer height = 22
      * tracing start max distance = 500 (how far are the clouds drawn)
    * Add SkyAtmosphere
    * Add Exponential Height fog
      * volumetric fog = on
      * albedo = black
      * fog inscattering color = black
      * directional inscattering = black
      * z position = -500 000
    * Add Directional light
      * atmospheric sunlight  = on
      * cast shadow on clouds = on
      * cast shadows on atmosphere = on
      * cast cloud shadows = on
      * light shaft occlusion = on
      * light shaft bloom = on
      * bloom scale 0.05
  * # **Increase quality**
    * Set r.VolumetricRenderTarget to 0 to start enabling cinematic quality results.
    * Set r.VolumetricCloud.HighQualityAerialPerspective to 1 to enable cinematic aerial perspective for clouds to use high quality ray tracing instead of low resolution LUTs.
    * Set the following on the Volumetric Cloud component:
      * Under the Cloud Tracing section, increase the sample count scale for View, Reflections, and Shadows. Sample counts can be increased for each of these scales using commands found in their tool tips.
      * Enable the Use per Sample Atmospheric Light Transmittance property to apply atmosphere transmittance per sample instead of using the Directional Light's global transmittance.
    * Set the following in your Volume Cloud Material using the Volumetric Advanced Material Output expression:
      * Apply ground lighting to the bottom of cloud layers, giving more shape and color to clouds in the scene.
        * Enable Ground Contribution from the Details panel. On the Volumetric Cloud component, use the Ground Albedo to specify the ground color used to light the cloud from below with respect to the sunlight and atmosphere.
      * Set the number of approximations used to simulate the multiple scattering of light through the cloud volume:
        * You can better simulate multiple scattering effects of light through the cloud volume by increasing the number of Multi Scattering Approximation Octaves used, up to a value of 2.
        * Light will scatter more through the cloud volume when applying additional octaves meaning that you'll want to adjust the Multi Scattering Contribution and Multi Scattering Occlusion values on the Volumetric Advanced Output expression to compensate.
    * Enable the following shadowing properties of Atmospheric Lights:
      * Set Cast Cloud Shadow to cast shadows from clouds onto scene elements and into the atmosphere.
      * Set Cast Shadows on Clouds to cast shadows from opaque objects onto cloud layers. The Directional Light must have a large enough Shadow Dynamic Distance to effectively shadow large objects onto clouds.
    * Choose between the following quality improvements for Sky Atmosphere based on your project needs:
      * Improve the overall quality of atmosphere rendering.
        * Set r.SkyAtmosphere.FastSkyLUT to 0. Disabling this optimization is slower to render, but produces fewer visual artefacts with high-frequency detail that can appear in places like the earth's shadow or scattering lobe.
      * Increase tracing quality of the atmosphere and sunlight shafts within the atmosphere.  Requires r.SkyAtmosphere.FastSkyLUT to be enabled.
        * Set r.SkyAtmosphere.AerialPerspectiveLUT.FastApplyOnOpaque to 0.
        * On the Sky Atmosphere component, use the quality slider Trace Sample Count Scale to adjust the number of samples used. If the max range isn't enough, use the command r.SkyAtmosphere.SampleCountMax to choose a higher limit and manually enter a value in the property field.
        * Improve the quality of sunlight shafts by increasing the LUT sizes set with the commands r.SkyAtmosphere.FastSkyLUT.Width and r.SkyAtmosphere.FastSkyLUT.Height.
        * Improve the quality of fog on opaque and transparent surfaces by increasing the size of r.SkyAtmosphere.AerialPerspectiveLUT.Width. Exercise caution when increasing this value because it is a 3D volume texture that increases memory consumption.