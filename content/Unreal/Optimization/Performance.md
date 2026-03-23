  * Scene cleanup
    * Delete unused stuff
      * right click folder and fix up redirectors
      * filter for unused assets (other filters) and delete
      * double check with right click - reference viewer
      * **as an alternative you can migrate to a new scene and it will only grab whats used**
  * Statistics
    * stat unit
      * If Draw is high - GPU is overloaded
      * If Game is high - CPU is slow at processing actors (most of the stuff is single threaded)
      * If GPU is high - graphics settings too high
    * stat scenerendering
      * drawcalls over 3-5k - cpu bound
      * Primitives rendered - can be too many objects
    * ProfileGPU
      * shows which part of the scene takes the most to render
    * Stat gpu (alternative to profile)
