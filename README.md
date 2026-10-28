<div align="center">

# Wiggy 0.1

**Soft body jiggle for characters and soft objects in Blender**<br>
Geometry Nodes on Blender's XPBD physics solver · Blender 5.2 or newer

[![Blender 5.2+](https://img.shields.io/badge/Blender-5.2%2B-orange?logo=blender&logoColor=white)](https://www.blender.org/)
[![Geometry Nodes · XPBD](https://img.shields.io/badge/Geometry%20Nodes-XPBD-blue)](#s2)
[![License: MIT + Commons Clause](https://img.shields.io/badge/License-MIT%20%2B%20Commons%20Clause-lightgrey.svg)](#s4)

</div>


---

---

## Contents

1. [What Wiggy is](#s1)
2. [How it works, in short](#s2)
3. [The demo scene in this file](#s3)
4. [License: MIT with Commons Clause](#s4)

**Guide** ([GUIDE ENG.md](GUIDE%20ENG.md)): quick start · every input of the modifier · colliders and force fields · several zones · baking, speed and frame rate · tuning tips and recipes · known limits

---

<a id="s1"></a>
## 1. What Wiggy is

Wiggy is one Geometry Nodes group, **"Wiggy 0.1"**, used as a modifier. It makes a part of a mesh jiggle with
real physics: breasts, belly, buttocks, thighs, cheeks, a tail, a jelly, a balloon, grass, a soft toy.

- The rest of the body follows the animation exactly; only the painted zone moves freely, held to the animation
  by soft "pins".
- It runs on Blender's own XPBD physics solver (the same one Blender's hair and cloth node assets use), every
  frame, in world space.
- It works with any armature, shape keys and object animation, because it takes the mesh as the modifier stack
  gives it.
- On top of the plain jiggle it has: volume keeping, heavy swing, gravity, colliders, contact shaping (push
  aside, cling, grip), self collision, squeeze, smoothing, slow motion and a distance limit.
- Every feature is off or neutral by default unless noted, so a new modifier gives a plain, stable jiggle.

---

<a id="s2"></a>
## 2. How it works, in short

Knowing this helps a lot with tuning.

- Every point of the zone is pinned to its animated position by a spring. **Softness × zone weight** decides how
  loose that spring is. Points with weight 0 are rigid: they are kinematic, nothing can move them.
- Between the points, edge and bending constraints keep the surface together (Stretching, Bending).
- **Mass** gives inertia: when the animation moves, the soft points lag behind, overshoot and swing back. That
  is the jiggle.
- The solver runs **Substeps** small steps per frame ([GUIDE 2.9](GUIDE%20ENG.md#s2-9)). Some features act inside every substep
  (contact shaping, volume, sway, wobble, distance limit, self collision), so they stay smooth and stable.
- **Preserve Volume** and **Sway** work per "island": every connected soft core (each breast, each buttock) is
  treated on its own, even inside one zone.
- The simulation runs in **world space**: moving or rotating the object jiggles it just like moving it with
  bones.
- XPBD stiffness is absolute, so behaviour depends on the real size of the object. The defaults are tuned for
  characters 1–2 m tall.

---

<a id="s3"></a>
## 3. The demo scene in this file

### Collections

| Collection | What is in it |
|---|---|
| **Wiggy Test** | The test sphere "Wiggy Softbody" hit by the animated "Wiggy Collider". The simplest setup, a good template. |
| **Wiggy Ring Demo** | A soft ring ("Wiggy Ring", skinned to the 12-bone "Wiggy Ring Rig") that a rod ("Wiggy Rod") passes through: contact shaping (Push Aside, Cling, Grip) and Preserve Volume. It has a bake. |
| **Wiggy Demos** | 12 stations in three rows, from simple to complex. The variants of a station stand side by side and move the same way, so the difference is easy to see. A sign behind each station explains it, a label under each variant names its setting. |

### Stations

**Row A — basics**

| | Station | Variants |
|---|---|---|
| A1 | Zone + Edge Fade | a hard Edit Mode group, Edge Fade 0 vs 4 |
| A2 | Softness | 0.12 / 0.22 / 0.32 |
| A3 | Wobble vs Damping | Wobble 1 / Wobble 0.3 / Damping 8 |
| A4 | Sway | 0 vs 0.9 |

**Row B — body and contact**

| | Station | Variants |
|---|---|---|
| B1 | Gravity + Max Distance | water balloons: no gravity / gravity / gravity + Max Distance 0.02 |
| B2 | Preserve Volume | a ball pressed into a cushion, 0 vs 1 |
| B3 | Self Collision | two soft lobes squeezed together, off / on |
| B4 | Wind | grass in steady wind and in gusts |

**Row C — advanced**

| | Station | Variants |
|---|---|---|
| C1 | Push Aside + Cling + Grip | a finger presses into a pad and drags the skin, off vs on |
| C2 | Squeeze | a soft ball flattened by a rigid wall of the same mesh, off vs on |
| C3 | Speed | animation 1x / 0.3x with Speed 1 / 0.3x with Speed 0.3 |
| C4 | Jelly creature | a rig hops it across a floor collider; two zones: body and antennae |

### How to watch

- Every station has its own camera in **"Demo Cameras"** ("Cam A1 …", etc.): select it and press
  <kbd>Ctrl</kbd> + <kbd>Numpad 0</kbd>. **"Cam Overview"** shows all.
- The whole file plays at about 0.24 s per frame. Exclude the collections you do not watch (checkbox in the
  Outliner) to play faster.
- Every demo object is a working example: select it and read its Wiggy modifier settings.

---


## A few words from the authors

*From @VOZHD1122 & @RoboDrozd:*

Thank you for choosing our project to help bring your creative visions to life and streamline your workflow.
This add-on is in the very early stages of development, so you may encounter bugs or other issues; we will do
our best to fix them based on your feedback. Please keep in mind that this add-on was created largely with the
help of a neural network.

> [!IMPORTANT]
> **ALWAYS BACK UP YOUR SCENES, JUST IN CASE!**

If you enjoy this add-on—crafted by a machine for 3D artists—and would like to see continued, steady support,
or if you’re interested in seeing new projects based on our ideas... Or, if you simply wish to support us—a
team of enthusiasts dedicated to making the already challenging lives of 3D artists a bit easier—we invite you
to join us on our social media and other platforms:

| Platform | Link | |
|---|---|---|
| **GitHub** | [github.com/RoboDrozd/wiggy](https://github.com/RoboDrozd/wiggy) | |
| **Discord** | [discord.gg/N6WaWncGzG](https://discord.gg/N6WaWncGzG) | Includes dedicated channels for discussing Wiggy |
| **Twitter** | [x.com/VOZHD1122](https://x.com/VOZHD1122) | DMs are open |
| **Boosty** | [boosty.to/bombino11](https://boosty.to/bombino11) | For supporting the project in Russian rubles |
| **Patreon** | [patreon.com/VOZHD11](https://www.patreon.com/VOZHD11) | For supporting the project in US dollars |

> [!NOTE]
> This README tells what Wiggy is, how it works, what the demo scene shows, and the license.
> **The guide** — quick start, every input of the modifier, colliders, zones, baking, tips and recipes —
> is in **[GUIDE ENG.md](GUIDE%20ENG.md)**.
> Both are also inside `Wiggy 0.1.blend`: open a **Text Editor** and pick **README ENG.txt** or
> **GUIDE ENG.txt** (Russian versions: **README RUS.txt**, **GUIDE RUS.txt**).

<a id="s4"></a>
## 4. License: MIT with Commons Clause

```text
MIT License with Commons Clause

Copyright (c) 2026 @VOZHD1122 & @RoboDrozd

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

---

Commons Clause License Condition v1.0

The Software is provided to you by the Licensor under the License (MIT),
subject to the following condition.

Without limiting other conditions in the License, the grant of rights under the
License will not include, and the License does not grant to you, the right to
Sell the Software.

For purposes of the foregoing, "Sell" means practicing any or all of the rights
granted to you under the License to provide to third parties, for a fee or
other consideration (including without limitation fees for hosting or
consulting/support services related to the Software), a product or service
whose value derives, entirely or substantially, from the functionality of the
Software. Any license notice or attribution required by the License must also
include this Commons Clause License Condition notice.

Software: WIGGY
License: MIT
Licensor: @VOZHD1122 & @RoboDrozd
```
