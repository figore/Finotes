* # Single layer water material
    * create new material and switch the shading model to single layer water
    * base color 0
    * opacity 0
    * Normal T_water_N with a flatten
    * ![image.png](image_1751447626549_0.png)
    * refraction 1
    * add single layer water material node under
    * **Absorption** has to be a very low number and indicates which color is removed in water
    * ![image.png](image_1751454104312_0.png)
    * **Scattering** uses the same nodes and adds mud with color
    * Problem with hard shadow![image.png](image_1751454659826_0.png)
    * enable raytraced shadows
* # Simple water
    - enable landmass and water in plugins
    - add button and type in lake
    - drag and drop water body lake to your terrain
    - absorbtion is the water color
    - [https://www.youtube.com/watch?v=DU42h7Dk4rI](https://www.youtube.com/watch?v=DU42h7Dk4rI)