  * ## Tire tracks in mud
    * Simulation
      * For ground Physics ▸ **Dynamic Paint** ▸ **Canvas** → **Surface Type: Waves**
      * ![image.png](image_1760201810874_0.png)
      * For tire same but pick brush and paint to mesh volume
    * Noise
      * Add a displace modifier and change the texture from the presets and scale
      * If there are any bugs check the brush mesh (can't be single sided and not closed)
    * Export
      * export as alembic and import as cache in UE
      * drag in actor and animate offset