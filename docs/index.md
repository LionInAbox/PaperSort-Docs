# PaperSort — Plugin Documentation

**Advanced 2D sprite sorting and altitude simulation for Paper2D.**

PaperSort gives you everything you need to handle Y-based depth sorting, height illusions, movement, and shadows for Paper2D games — all from simple, drop-in components.

![Plugin overview banner](insert_image: banner showing a 2D scene with correctly sorted sprites at different depths)

---

## Table of Contents

1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Quick Start](#quick-start)
4. [Sorting Components](#sorting-components)
   - [PaperSort](#papersort)
   - [PaperBox](#paperbox)
5. [Height & Flight](#height--flight)
   - [PaperFloat](#paperfloat)
6. [Shadows](#shadows)
   - [PaperShadow](#papershadow)
7. [Sample Setups](#sample-setups)
   - [PaperZD Character with PaperSort + PaperShadow](#paperzd-character-with-papersort--papershadow)
   - [PaperZD Character with PaperFloat + PaperShadow + PaperSort](#paperzd-character-with-paperfloat--papershadow--papersort)
8. [Blueprint API Reference](#blueprint-api-reference)
9. [FAQ](#faq)

---

## Requirements

- Unreal Engine 5.x
- Paper2D plugin enabled
- **All sprite and flipbook materials must be translucent** (`TranslucentUnlitSpriteMaterial` or `TranslucentLitSpriteMaterial`). Masked and opaque materials do not support sort ordering.

## Installation

Install PaperSort from **Fab** and enable it in your project. All components appear in the **Add Component** menu under the **PaperSort** category.

---

## Quick Start

1. Set your sprite/flipbook materials to a **translucent material** (`TranslucentUnlitSpriteMaterial`).
2. Add a **PaperSort** component to your actor.
3. Apply this process to all sprites and flipbooks in the scene that you want sorted.
4. Set the background tile map's **Translucent Sort Priority** to a low enough number, e.g. **-1000000**.
5. Press Play. Your sprites now sort correctly based on Y position.

![Quick Start result](insert_image: before/after showing sprites sorting incorrectly vs correctly after adding PaperSort)

PaperSort automatically finds all sprites and flipbooks on the actor and handles sorting. No hierarchy setup needed.

Sprites and flipbooks without a PaperSort component won't be included in the sorting system, so make sure every actor you want sorted has one.

The background tile map needs a Translucent Sort Priority low enough that sorted sprites never reach it. A value like -1000000 means your sprites would have to travel to Y position -1000000 before they'd appear behind the tile map — which is unlikely to happen.

---

## Sorting Components

The PaperSort plugin comes with two components, **PaperSort** and **PaperBox**, that both automate 2D sprite sorting on the Y axis.

PaperSort's and PaperBox's sorting mechanisms don't work together, since they're two separate systems. For globally applied sorting it's therefore recommended to use either only PaperSort or PaperBox in the same level.

#### Comparison: PaperSort vs. PaperBox Component

| | PaperSort | PaperBox |
|---|---|---|
| **Effort** | Drop in, done (+ set background map's Translucent Sort Priority to -1000000) | Requires project settings + hierarchy setup |
| **Performance** | Good — up to 1,000+ sprites | Excellent — 10,000+ sprites |
| **How it sorts** | Sets TranslucentSortPriority per sprite | Uses UE's built-in Translucent Sort Policy |
| **Runtime cost** | Centralized subsystem tick | Zero when Update In Game is off; per-frame when on |
| **Best for** | Scenes with up to 1,000 moving sprites, like RPGs and almost every normal 2D game | Scenes with up to 10,000 moving sprites, like bullet hell and survival roguelite |

---

### PaperSort

Dynamic, easy, zero-configuration sorting. Works with PaperZD characters by default.

**Multiple PaperSort on one actor:** Each PaperSort manages the sprites and flipbooks in its parent's subtree, stopping at branches claimed by another PaperSort and another PaperSort's child sprites. This lets you sort different sprite groups independently on the same actor.

**Debug visualization (editor and viewport only):** A crosshair and dashed sorting line are drawn in the viewport. This line visualizes the reference point where the sorting happens. Anything above that point will be sorted behind, and anything below sorted in front.

![PaperSort debug visualization](insert_image: editor viewport showing the crosshair and dashed sorting line on a character)

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
3. The box auto-sizes to fit child sprites and flipbooks in the editor (when **Auto Fit Box** is enabled).

![PaperBox hierarchy](insert_image: component hierarchy showing sprites as children of PaperBox)

**PaperZD characters:** Create an additional flipbook component, so you can add it as a child of PaperBox, use it for PaperZD animations, and hide the original built-in flipbook.

#### Properties

| Property | Description |
|----------|-------------|
| **Update In Game** | Recalculate bounding box every frame at runtime. Enable for sprites that change size or altitude during gameplay (animations with varying dimensions, flying sprites). Disable for static props and backgrounds — the bounds are set once and never tick, giving you zero runtime cost. |
| **Auto Fit Box** | Editor only. When enabled, the box automatically adjusts its bounds to fit all child sprites and flipbooks whenever they are added or moved. When disabled, the box stays where you left it — an **Auto Fit** button available with `Auto Fit Box` unchecked. It lets you trigger a one-time auto fit, after which you can continue to adjust the box manually. |
| **Box Padding** | Extra padding around the auto-calculated extents (only when Update In Game is off). |

---

## Height & Flight

### PaperFloat

Creates the visual illusion of height in a 2D world by offsetting sprite/flipbook Y position based on the collision component's Z position. When a character walks up a ramp or starts floating, it increases its Y position to simulate levitation. It also has automatic shadow handling: keep the selected shadow sprite at ground level at all times, whether the character is floating, jumping, falling or walking.

PaperFloat is the most feature-rich component in the plugin. It ties together collision, movement, and shadows into a unified height system.

![PaperFloat in action](insert_image: side by side showing a character on flat ground vs walking up a ramp, with shadow staying on the ground)

#### Setup

1. Add **PaperFloat** to your actor.
2. Create a new flipbook component and make it a **child** of PaperFloat. Use this flipbook for your animations. Hide the original built-in flipbook — the affected sprite/flipbook needs to be a child of PaperFloat for the Y offset to work.
3. Add a collision component to the actor.
4. Select the collision in PaperFloat's **Collision Component** dropdown (auto-selects the first collision component it finds).
5. Optionally add a **PaperShadow** as a child and select it in the **Shadow Component** dropdown.

![PaperFloat hierarchy](insert_image: component hierarchy showing PaperFloat with child flipbook and PaperShadow)

#### Movement Mode

PaperFloat supports two modes:

- **Walk** — Altitude is derived automatically from the collision component's Z position. When the collision moves up (walking up a ramp, being pushed upward), the altitude increases and the sprites rise on screen. The Altitude field is read-only in this mode.
- **Float** — Altitude is set freely via Blueprint or the Details panel. Use this for flying characters, hovering, or any situation where you want direct control over height.

#### Properties

| Property | Description |
|----------|-------------|
| **Altitude** | Current altitude. Read-only in Walk mode, editable in Float mode. |
| **Y Scale Factor** | How much Y offset per unit of altitude. Default 1.0. |
| **Movement Mode** | **Walk**: altitude from collision component's Z. **Float**: set altitude freely. |

#### Ground Reference

| Property | Description |
|----------|-------------|
| **Use Initial Ground as Zero** | The ground beneath the collision component at BeginPlay becomes altitude zero. A character starting on a raised platform reads altitude 0 instead of the platform's Z value. |
| **Teleport to Ground on Start** | Snaps the collision to the ground on play start. Prevents unwanted falling when starting the game. |

#### Ledge Push Off Prevention

When a character walks off a ledge, the collision system can push them sideways unnaturally. PaperFloat can cancel this on the exact frame the actor becomes airborne.

| Property | Description |
|----------|-------------|
| **Prevent Ledge Push Off** | Cancels sideways velocity when walking off ledges. Doesn't affect intentional jumps. |
| **Movement Component** | Which movement component to monitor for grounded/airborne state. Auto-selects first found. |

#### Shadow Tracking

When a character is airborne, the shadow stays on the ground below them automatically.

| Property | Description |
|----------|-------------|
| **Shadow Component** | A sprite or flipbook to use as ground shadow. Stays at ground level when airborne. Auto-selects PaperShadow if one exists. |
| **Shadow Trace Distance** | Max downward distance to find ground for shadow placement. Shadow hides if no ground is found. |
| **Use Collision Shape for Shadow Positioning** | Full shape sweep instead of center ray. Slighly more expensive, but more accurate shadow placement near ledges, needed when jumping or flying over ramps and elevated areas. |

#### Debug (Editor Only)

| Property | Description |
|----------|-------------|
| **Draw Height Lines** | Dashed line showing collision component's height level projected into y space in the viewport. |
| **Height Line Color / Thickness** | Customize the debug line appearance. |

![PaperFloat Details panel](insert_image: screenshot of the PaperFloat Details panel in the editor)

---

## Shadows

### PaperShadow

Procedurally generated shadow sprite — no texture assets needed.

![PaperShadow examples](insert_image: a few shadow variations showing different radii, blur, opacity, and pixelation settings, and in different situations: normal, jump, on ramps, and flight)

#### Setup

1. Add **PaperShadow** as a child of **PaperFloat**.
2. Select it in PaperFloat's **Shadow Component** dropdown.
3. Adjust to taste.

| Property | Description |
|----------|-------------|
| **Radius X / Radius Y** | Ellipse dimensions in Unreal units. |
| **Blur Radius** | Edge feather/softness. |
| **Shadow Opacity** | 0 = transparent, 1 = opaque. |
| **Shadow Color** | Shadow color. Black by default. |
| **Pixelation** | Pixel size multiplier. Higher = chunkier retro pixels. Doesn't change world size. |

---

## Sample Setups

### PaperZD Character with PaperSort + PaperShadow

A standard PaperZD character with automatic Y sorting and a ground shadow. No height/flight — flat ground only.

A second PaperSort is added as a child of PaperShadow. This gives the shadow its own independent sort priority. Place this PaperSort slightly higher (on the y axis) than the main PaperSort — this way the shadow is always sorted behind the character.

**Component hierarchy:**

```
Actor (PaperZD Character)
└── CapsuleComponent                   ← collision component
    ├── PaperFlipbook (PaperZD animations — built-in, used as normal)
    ├── PaperSort                      ← sorts the character
    └── PaperShadow                    ← ground shadow
        └── PaperSort                  ← sorts the shadow (place slightly higher than main PaperSort)
```

![Sample Setup 1 hierarchy](insert_image: screenshot of this component hierarchy in the Blueprint editor)

**Steps:**

1. Set your flipbook material to `TranslucentUnlitSpriteMaterial`.
2. Add **PaperSort** to the actor as a child of the CapsuleComponent.
3. Add **PaperShadow** to the actor. Position it at the character's feet.
4. Add a second **PaperSort** as a child of PaperShadow. Position it slightly higher than the main PaperSort so the shadow always renders behind the character.
5. Adjust shadow radius, blur, and opacity.
6. Done — sorting and shadow work automatically, with the shadow always behind.

![Sample Setup 1 result](insert_image: in-game screenshot showing the PaperZD character with shadow, sorted correctly among other sprites)

### PaperZD Character with PaperFloat + PaperShadow + PaperSort

A PaperZD character that can walk up ramps or fly, with a shadow that stays on the ground and correct Y sorting.

**Component hierarchy:**

```
Actor (PaperZD Character)
Actor (PaperZD Character)
└── CapsuleComponent                   ← collision
    ├── PaperFlipbook (original built-in — set to INVISIBLE)
    ├── PaperSort                      ← sorting
    └── PaperFloat                     ← height system
        ├── PaperFlipbook (new — used for PaperZD animations)
        └── PaperShadow                ← ground shadow
```

![Sample Setup 2 hierarchy](insert_image: screenshot of this component hierarchy in the Blueprint editor)

**Steps:**

1. Set flipbook materials to `TranslucentUnlitSpriteMaterial`.
2. Add **PaperSort** to the actor.
3. Add **PaperFloat** to the actor.
4. Create a **new Flipbook** component as a child of PaperFloat. Use this for your PaperZD animations.
5. **Hide** the original built-in flipbook (set Visible to false).
6. Add a collision component to the actor.
7. In PaperFloat's Details panel, select the collision component in the **Collision Component** dropdown.
8. Add **PaperShadow** as a child of PaperFloat.
9. Select the PaperShadow in PaperFloat's **Shadow Component** dropdown.
10. Adjust shadow and float settings to taste.

![Sample Setup 2 result](insert_image: in-game screenshot showing the character walking up a ramp with shadow on the ground below)

---

## Blueprint API Reference

| Function | Description |
|----------|-------------|
| `GetAltitude()` | Returns the current altitude value. |
| `SetAltitude(Z)` | Sets the altitude by moving the actor so the collision component's bottom sits at the given world Z. |
| `IncreaseAltitude(Delta)` | Increases the current altitude by the given amount. Use this to smoothly raise or lower a character over time — for example, calling it every frame with a small delta to create a gradual lift-off or landing effect. |

---

## FAQ

**Can I mix PaperSort and PaperBox in the same level?**
You can, but they don't combine. Pick one approach per level, for a global sorting system that sorts all sprites.

**Does PaperSort work with PaperZD?**
Yes, by default. No extra setup.

**Does PaperBox work with PaperZD?**
Yes, but you need to create an additional flipbook, so you can add it as a child of PaperBox, use it for PaperZD animations, and hide the original built-in flipbook.

**Do I need PaperFloat for basic sorting?**
No. PaperFloat is only for height effects (ramps, flying, shadows). For flat sorting, PaperSort or PaperBox alone is enough.

**Sprites rendering in wrong order?**
Check that your materials are **translucent** (`TranslucentUnlitSpriteMaterial` or `TranslucentLitSpriteMaterial`). Masked and opaque materials don't support sort ordering.

---

*PaperSort Plugin — by Lion Box*
