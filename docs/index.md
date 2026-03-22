# PaperSort — Plugin Documentation

**Advanced 2D sprite sorting and altitude simulation for Paper2D.**

PaperSort gives you everything you need to handle Y-based depth sorting, height illusions, collision, and shadows for Paper2D games — all from simple, drop-in components.

![Plugin overview banner](insert_image: banner showing a 2D scene with correctly sorted sprites at different depths)

---

## Table of Contents

1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Quick Start](#quick-start)
4. [PaperSort and PaperBox](#papersort-and-paperbox)
5. [PaperFloat](#paperfloat)
6. [PaperShadow](#papershadow)
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
2. Add a **PaperSort** component to your actor and move the **PaperSort** component to your desired sort origin position.
3. Apply this process to all sprites and flipbooks in the scene that you want sorted.
4. Set the background tile map's **Translucent Sort Priority** to a low enough number to always keep it in the background, e.g. **-1,000,000**.
5. Press Play. Your sprites now sort correctly based on Y position.

![Quick Start result](insert_image: before/after showing sprites sorting incorrectly vs correctly after adding PaperSort)

PaperSort automatically finds all sprites and flipbooks on the actor and handles sorting. No hierarchy setup needed.

Sprites and flipbooks without a PaperSort component won't be included in the sorting system, so make sure every actor you want sorted has one.

The background tile map needs a Translucent Sort Priority low enough that sorted sprites never reach it. A value like -1000000 means your sprites would have to travel to Y position -1000000 before they'd appear behind the tile map — which is unlikely to happen.

---

## PaperSort and PaperBox (Automatic sprite sorting components)

The PaperSort plugin comes with two sorting components, **PaperSort** and **PaperBox**, that both automate 2D sprite sorting on the Y axis.

PaperSort's and PaperBox's sorting mechanisms are two separate systems and don't work together. PaperSort sets the Translucency Sort Priority of each sprite to it's y value. PaperBox uses Unreal Engine's built-in Sort by Axis ordering. PaperSort is much easier to set up than PaperBox, but is less performant (up to +1000 moving sprites, versus PaperBox several 10000 moving sprites)

#### Comparison: PaperSort vs. PaperBox Component

| | PaperSort | PaperBox |
|---|---|---|
| **Effort** | Drop in, done (+ set background map's Translucent Sort Priority to -1,000,000) | Requires project settings + hierarchy setup + positioning sprites around box center |
| **Performance** | Good — up to 1,000+ sprites | Excellent — 10,000+ sprites |
| **How it sorts** | Sets TranslucentSortPriority per sprite | Uses UE's built-in Translucent Sort Policy |
| **Runtime cost** | Centralized subsystem tick | Zero when Update In Game is off; per-frame when on |
| **Best for** | Scenes with up to 1,000 moving sprites, like RPGs and almost every normal 2D game | Scenes with up to 10,000 moving sprites, like bullet hell and survival roguelite |

→ [Full documentation: Sorting Components](papersort.md)

---

## PaperFloat (Height and Flight Component)

Creates the visual illusion of height in a 2D world by offsetting sprite/flipbook Y position based on the collision component's Z position. When a character walks up a ramp or starts floating, it increases its Y position to simulate levitation. It also has automatic shadow handling: keep the selected shadow sprite at ground level at all times, whether the character is floating, jumping, falling or walking.

→ [Full documentation: PaperFloat](paperfloat.md)

---

## PaperShadow (Generated shadow sprite)

Procedurally generated ellipse-shaped shadow sprite — no texture assets needed. Works perfectly in combination with PaperFloat.
Set width, height, opacity, blur radius, pixelation to your liking.

→ [Full documentation: PaperShadow](papershadow.md)

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
