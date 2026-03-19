# PaperShadow — Shadows

Procedurally generated, ellipsed-shaped shadow sprite — no texture assets needed.

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
