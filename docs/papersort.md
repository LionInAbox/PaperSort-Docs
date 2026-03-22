# PaperSort & PaperBox — Sorting Components

The PaperSort plugin comes with two sorting components, **PaperSort** and **PaperBox**, that both automate 2D sprite sorting on the Y axis.

PaperSort's and PaperBox's sorting mechanisms are two separate systems and don't work together. PaperSort sets the Translucency Sort Priority of each sprite to it's y value. PaperBox uses Unreal Engine's built-in Sort by Axis ordering. PaperSort is much easier to set up than PaperBox, but handles fewer sprites — around 1,000+ moving sprites versus several 10,000s with PaperBox. PaperBox also offers slightly better pixel precision for sorting.
For globally applied sorting it's recommended to use either only PaperSort or PaperBox in the same level.

#### Comparison: PaperSort vs. PaperBox Component

| | PaperSort | PaperBox |
|---|---|---|
| **Effort** | Drop in, done (+ set background map's Translucent Sort Priority to `-1,000,000`) | Requires project settings + hierarchy setup |
| **Performance** | Good — up to 1,000+ sprites | Excellent — 10,000+ sprites |
| **How it sorts** | Sets TranslucentSortPriority per sprite | Uses UE's built-in Translucent Sort Policy |
| **Runtime cost** | Centralized subsystem tick | Zero when Update In Game is off; per-frame when on |
| **Best for** | Scenes with up to 1,000 moving sprites, like RPGs and almost every normal 2D game | Scenes with up to 10,000 moving sprites, like bullet hell and survival roguelite |

---

### PaperSort

Dynamic, easy, zero-configuration sorting. Works with PaperZD characters by default.

#### Setup

1. Add a PaperSort component to the root component of every actor in your scene, and place it at your desired sort origin position. 
2. Set the background map's translucency sort priority to a large negative number like `-1,000,000`*. 
3. You're good to go, the sorting happens automatically!

#### Multiple PaperSort on one actor
Each PaperSort manages the sprites and flipbooks in its parent's subtree, stopping at branches claimed by another PaperSort and another PaperSort's child sprites. This lets you sort different sprite groups independently on the same actor.

**Debug visualization (editor and viewport only):** A crosshair and dashed sorting line are drawn in the viewport. This line visualizes the reference point where the sorting happens. Anything above that point will be sorted behind, and anything below sorted in front.

![PaperSort debug visualization](insert_image: editor viewport showing the crosshair and dashed sorting line on a character)

>***\* About the value:*** Your sprites will most likely never reach a y position of `-1,000,000`, so the background is guaranteed to sort behind everything else. Adjust as needed.

---

### PaperBox

Bounding-box sorting. More setup, but uses Unreal Engine's built-in sorting system for maximum performance.

#### Project Settings (required once)

1. Go to **Project Settings → Rendering → Translucency**.
2. Set **Translucent Sort Policy** to `Sort Along Axis`.
3. Set the sort axis to **`0, 1, 0`** (Y axis).

![Project Settings](insert_image: screenshot of the Project Settings panel with Translucent Sort Policy and sort axis highlighted)

#### Setup

1. Add a **PaperBox** component to your actor.
2. Make your sprites and flipbooks **children** of PaperBox in the component hierarchy.
3. Place them so that their sorting origin is the center of the PaperBox (the center is marked with a base line and the box wireframe).
4. The box auto-sizes to fit child sprites and flipbooks in the editor (when **Auto-Fit Box** is enabled).

![PaperBox hierarchy](insert_image: component hierarchy showing sprites as children of PaperBox)

**PaperZD characters:** Create an additional flipbook component, so you can add it as a child of PaperBox, use it for PaperZD animations, and hide the original built-in flipbook.

#### Properties

| Property | Description |
|----------|-------------|
| **Update In Game** | Recalculate bounding box every frame at runtime. Enable for sprites that change size or altitude during gameplay (animations with varying dimensions, flying sprites). Disable for fixed-size animations, static props and backgrounds — the bounds are set once and never tick, giving you zero runtime cost. When enabled, all editor-only options below are greyed out. |
| **Auto-Fit Box** | Editor only. When enabled, the box continuously adjusts its bounds to fit all child sprites and flipbooks whenever they are added or moved. Greyed out when Update In Game is enabled. |
| **Box Padding** | Extra padding around the auto-calculated extents. Only available when Auto-Fit Box is enabled (and Update In Game is disabled). |
| **Auto-Fit Once** | Button. Performs a one-time fit of the box to child bounds (without padding). Only available when Auto-Fit Box is disabled and Update In Game is disabled. After fitting, you can continue to adjust the box manually via Box Extent. |
| **Box Extent** | Manual XY size control. Only available when both Auto-Fit Box and Update In Game are disabled. Lets you set the box dimensions directly. |
