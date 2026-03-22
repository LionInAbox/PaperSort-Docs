# PaperFloat — Height & Flight

Creates the visual illusion of height in a 2D world by offsetting sprite/flipbook Y position based on the collision component's Z position. When a character walks up a ramp or starts floating, it increases its Y position to simulate levitation. It also has automatic shadow handling: keep the selected shadow sprite at ground level at all times, whether the character is floating, jumping, falling or walking.

PaperFloat is the most feature-rich component in the plugin. It ties together collision, movement, and shadows into a unified height system.

![PaperFloat in action](insert_image: side by side showing a character on flat ground vs walking up a ramp, with shadow staying on the ground)

#### Setup

1. Add **PaperFloat** to your actor.
2. Create a new flipbook component and make it a **child** of PaperFloat. Use this flipbook for your animations. Hide the original built-in flipbook — the affected sprite/flipbook needs to be a child of PaperFloat for the Y offset to work.
3. Add a collision component to the actor.
4. Select the collision in PaperFloat's **Collision Component** dropdown (auto-selects the first collision component it finds).
5. Optionally add a **PaperShadow** as a child and select it in the **Shadow Component** dropdown.

> ⚠️ **Deactivate sprite and flipbook collision.** Paper2D sprites and flipbooks have collision enabled by default, using an image-bound polygon that approximates the sprite outline. This polygon can physically push the character up and down — which PaperFloat interprets as altitude changes, causing visible jitter. Disable collision on all sprites and flipbooks in your scene.

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

When a character is airborne, the shadow stays on the ground below them automatically. The shadow can also shrink and fade with distance to create a natural sense of depth.

| Property | Description |
|----------|-------------|
| **Shadow Component** | A sprite or flipbook to use as ground shadow. Stays at ground level when airborne. Auto-selects PaperShadow if one exists. |
| **Shadow Trace Distance** | Max downward distance to find ground for shadow placement. Shadow hides if no ground is found. |
| **Use Collision Shape for Shadow Positioning** | Full shape sweep instead of center ray. Slightly more expensive, but more accurate shadow placement near ledges, needed when jumping or flying over ramps and elevated areas. |
| **Shrink with Distance** | The shadow scales down linearly as distance to the ground increases. At ground level the shadow is full size; at the vanish distance it disappears. Enabled by default. |
| **Vanish Distance** | The distance at which the shadow reaches zero scale. Default 500. |
| **Fade Opacity with Distance** | The shadow's opacity fades linearly as distance to the ground increases. Stacks with the base opacity set on the shadow component. Enabled by default. |
| **Opacity Vanish Distance** | The distance at which the shadow becomes fully transparent. Default 300. |

#### Debug (Editor Only)

| Property | Description |
|----------|-------------|
| **Draw Height Lines** | Dashed line showing collision component's height level projected into y space in the viewport. |
| **Height Line Color / Thickness** | Customize the debug line appearance. |

![PaperFloat Details panel](insert_image: screenshot of the PaperFloat Details panel in the editor)
