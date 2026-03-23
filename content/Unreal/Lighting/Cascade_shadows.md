  * ## Cascaded Shadow Maps (UE)
    * Purpose : Used for directional lights (sun/moon). Splits camera view into slices (cascades), each with its own shadow map.
    * Problem they solve : One shadow map can’t cover both near + far shadows with enough detail.
    * How they work
      * Near cascades : small area, high resolution → sharp close shadows
      * Far cascades : large area, lower resolution → softer distant shadows
      * Engine blends between cascades to hide transitions
    * Performance : More cascades = higher quality but more GPU cost. Usually 3–4 cascades is a good balance.
  * ## Directional Light Settings
    * **Dynamic Shadow Distance** : max distance cascaded shadows render (after that switches to far shadow cascades **or nothing and then you have no shadows on distant islands**)
    * **Num Dynamic Shadow Cascades** : number of cascades (1–4); more = sharper + heavier
    * **Distribution Exponent** : controls detail distribution; higher = more near detail
    * **Transition Fraction** : size of blend between cascades; 0.2–0.3 smooth
    * **Far Shadow Cascade Count** : extra far cascades for very large worlds; usually off
    * **Far Shadow Distance** : distance far shadows extend