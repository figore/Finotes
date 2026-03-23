# Merged to instance
### 1. Separate the Merged Object

First, you need to break that single object back down into 10 individual pieces.

- Select your merged object and press **`Tab`** to enter Edit Mode.
- Press **`A`** to select all vertices.
- Press **`P`** to bring up the Separate menu.
- Select **By Loose Parts**.
### 2. Convert Them to Instances (Link Object Data)

Now, press **`Tab`** to go back to Object Mode. You currently have 10 separate objects, but Blender evaluates them as 10 entirely unique meshes. We need to tell Blender they are all the exact same thing.

- Select all 10 of your new objects in the viewport.
- Make sure one of them is the **Active Object** (it will have a brighter yellow/orange outline than the others). If none are bright orange, simply **`Shift + Left Click`** on one of them.
- Press **`Ctrl + L`** to bring up the Link/Transfer Data menu.
- Select **Link Object Data**.

# Baking 360 HDR Environment

### 1. Camera Setup

-   **Add Camera:** Shift + A → Camera. Place at scene center (approx `Z: 1.5m`).
-   **Rotation:** Set strict rotation to level the horizon.
    - **X:** `90°`
    - **Y:** `0°`
    - **Z:** `0°` (or rotate to align "front")
-   **Lens Settings:**
    - Select Camera → **Object Data Properties** (Green Camera Icon).
    - **Type:** `Panoramic`
    - **Panorama Type:** `Equirectangular`
### 2. Render Settings

-   **Engine:** Set to **Cycles** (Eevee supports it, but Cycles handles light bounces better for HDRs).
-   **Resolution:** Must be **2:1 Aspect Ratio**.
    **X:** `4096` (High) or `2048` (Draft)
    **Y:** `2048` (High) or `1024` (Draft)
    
### 3. Output Format

-   Go to **Output Properties** (Printer Icon).
-   **File Format:** `Radiance HDR` (.hdr) or `OpenEXR` (Float Full).
-   **Color Depth:** `Float (Full)` or `Half` (32-bit/16-bit).
    > **Note:** Do not use PNG/JPG; you will lose lighting data.
### 4. Render & Save

-   Press `F12` to Render.
-   Once finished: `Image` → `Save As...`
-   Save as `.hdr` file.
### 💡 Tips for WebGL

- **Invisible Floor:** If you want shadows but not the floor mesh:
    
    - Select Floor → Object Properties → Visibility → Check **Shadow Catcher**.
        
- **Compression:** Convert the resulting `.hdr` to `.exr` or `.ktx2` / `.env` for web performance (Three.js/Babylon).
