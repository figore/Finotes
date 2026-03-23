# Blender Cycles Viewport Optimization (RTX A6000 & Xeon)

**Hardware Profile:** NVIDIA RTX A6000 (48GB VRAM) & Intel Xeon Gold 5220R (2.2GHz base)
**Focus:** High-performance viewport evaluation for heavy automotive models and complex environments.

---

## 1. System Preferences (Managing the CPU Bottleneck)
The RTX A6000 is a raytracing powerhouse, but the Xeon Gold’s lower single-core clock speed (2.2GHz) can bottleneck viewport updates (BVH building, modifier evaluation). 
* **Go to:** `Edit > Preferences > System > OptiX`
* **Action:** Check **ONLY** the RTX A6000. **Uncheck** the Xeon CPU. 
* **Why:** If the CPU is checked, the incredibly fast GPU will pause and wait for the slower CPU threads to finish rendering their assigned tiles, causing severe viewport stutter.

## 2. Denoiser Setup
* **Viewport Denoising:** Use **OptiX**. It leverages the A6000's Tensor Cores for near-instant updates while panning the camera or adjusting lighting.
* **Final Render Denoising:** Use **OpenImageDenoise (OIDN)** set to *Accurate*. OptiX can smudge high-frequency details (metallic flakes, tight panel gaps, crisp reflections). OIDN preserves these sharp details much better and is fully GPU-accelerated.
* *Note: Never use Eevee denoisers or the Compositor denoise node with OptiX/OIDN for real-time workflows, as AI denoisers rely on Cycles path-tracing data.*

## 3. Viewport Render Settings
Let the AI denoiser do the heavy lifting to keep the viewport lightning fast.
* **Noise Threshold:** `0.1` (Tells Cycles to stop calculating pixels early and hand off to OptiX).
* **Max Samples:** `32` to `64` is usually plenty for live previewing materials.

## 4. Light Paths & Bounces
Heavy environment scenes and complex automotive glass require specific bounce limits to look realistic without killing performance. Keep these as low as possible.
* **Go to:** `Render Properties > Light Paths > Max Bounces`
* **Total:** `4` to `6`
* **Diffuse:** `1` or `2` (Plenty for asphalt, concrete, and matte surfaces)
* **Glossy:** `2` or `3` (Critical for clear coats, chrome, and metallic paint reflections)
* **Transmission:** `4` to `6` (Minimum required for light to pass through a car windshield, bounce around the interior, and exit the opposite side)
* **Transparent:** `4` (For foliage alphas or chainlink fences in the environment)

## 5. Lighting Optimization
* **Light Tree:** * **Enable** if the environment has dozens/hundreds of light sources (streetlights, emissive signs, complex studio rigs).
	* **Disable** if lighting is primarily driven by a single HDRI and a Sun lamp (adds unnecessary CPU calculation overhead).
* **Fast GI Approximation:** Enable this under the `Simplify` panel to replace complex indirect bounces with ambient occlusion. Great for quick lighting checks in dense scenes.

## 6. Scene Management & Geometry Pipeline
To bypass the Xeon's single-core evaluation speed limit, reduce the math it needs to do every time the viewport updates.
* **Instances vs. Duplicates:** * Use **Alt+D (Linked Duplicate / Instance)** for repeated geometry (wheels, brake calipers, streetlights, barriers). The CPU calculates the mesh once and tells the GPU where to draw it.
	* Avoid **Shift+D (Duplicate)** for heavy props, as it multiplies the CPU's workload.
* **Modifiers:** * The **Mirror Modifier** (and Array/Sub-D) generates unique geometry dynamically. The CPU recalculates this every frame update. 
	* For static geometry that won't be animated, apply the modifiers. Raw meshes feed instantly to the GPU and are cleaner for exporting to external engines like Unreal.
* **Texture Limits (Simplify):** Enable `Simplify` and cap Viewport Textures at `1024` or `2048`. This prevents viewport lock-ups when loading gigabytes of 4K/8K environment textures from the drive into memory, while keeping final renders at full resolution.