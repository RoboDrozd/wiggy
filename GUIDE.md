<div align="center">

# Wiggy 0.1 — Guide

**Quick start · every input of the modifier · colliders · zones · baking · tips and recipes**

</div>

> [!NOTE]
> What Wiggy is, how it works, the demo scene, the authors' words and the license are in
> **[README ENG.md](README%20ENG.md)**. The same guide is inside `Wiggy 0.1.blend`: open a **Text Editor**
> and pick **GUIDE ENG.txt** (Russian: **GUIDE RUS.txt**).

---

## Contents

1. [Quick start](#s1)
2. [The modifier panel, input by input](#s2)
   - 2.1 [Main inputs (top of the panel)](#s2-1)
   - 2.2 [Dynamics](#s2-2)
   - 2.3 [Shape](#s2-3)
   - 2.4 [Gravity](#s2-4)
   - 2.5 [Collisions](#s2-5)
   - 2.6 [Self Collision](#s2-6)
   - 2.7 [Squeeze](#s2-7)
   - 2.8 [Smoothing](#s2-8)
   - 2.9 [Solver](#s2-9)
3. [Colliders and force fields](#s3)
4. [Several zones on one body](#s4)
5. [Baking, speed and frame rate](#s5)
6. [Tuning guide: tips from development](#s6)
   - 6.1 [Recipes](#s6-1)
   - 6.2 [Problem → fix](#s6-2)
7. [Known limits](#s7)

---

<a id="s1"></a>
## 1. Quick start

1. **Make a vertex group for the part that should jiggle.**
   - Weight 1 = fully soft, weight 0 = follows the animation rigidly.
   - Paint it in Weight Paint mode for smooth borders, or assign it in Edit Mode and use Edge Fade
     ([2.1](#s2-1)) to soften the border.
   - Do **NOT** give the group the name of a bone (see [7](#s7)).
2. **Add the modifier.**
   - In another file, first bring the group in: *File › Append › this file (Wiggy 0.1.blend) › NodeTree ›
     "Wiggy 0.1"*. In this file it is already there.
   - *Properties › Modifiers › Add Modifier › Generate › Geometry Nodes*, then pick **"Wiggy 0.1"** in the
     modifier's node group field.
   - Move the modifier right after the Armature modifier and before Corrective Smooth / Subdivision (the
     modifier's menu has *Move Up* / *Move to First*, or drag it by its handle).
   - If you will use Self Collision, turn on **"Add Rest Position"** for the object: *Object Data properties ›
     Shape Keys panel* ([2.6](#s2-6)).
3. **Set Zone to your vertex group:** click the small toggle button to the right of the Zone value, then pick
   the group name.
4. **Press Play from the start frame.** The simulation always starts at the scene's start frame; jumping back to
   it resets the simulation.
5. **Tune Softness** ([2.2](#s2-2)) first, then Wobble and Sway, then the rest.
6. **When you like it, bake it** ([5](#s5)). Baked frames play back instantly.

**Modifier order on a character:**

```text
Armature  →  Wiggy (one per zone)  →  Corrective Smooth  →  Subdivision Surface
```

> [!TIP]
> Simulate on the low-resolution mesh; subdivide the result.

---

<a id="s2"></a>
## 2. The modifier panel, input by input

Every input below is listed with its **default** and **range**, what it does, and tips (💡).

Inputs that show a small toggle button next to the value can take a vertex group or attribute instead of one
number (for example Zone, Mass, Stretching, Bending, Damping, Thickness, Friction). Then the value varies over
the mesh.

> [!NOTE]
> Defaults of a new modifier are the author's preset: **Softness 0.2, Mass 6, Stretching 0.1, Bending 0.2,
> Sway 0.8, Squeeze Depth 1.**

<a id="s2-1"></a>
### 2.1 Main inputs (top of the panel)

| Input | Default | Range |
|---|---|---|
| Simulate | on | — |
| Zone | `0.333` | 0 – 1 |
| Edge Fade | `0` | 0 – 64 |
| Influence | `1` | 0 – 1 |

#### Simulate
**Default** on

Runs the physics. Off, the body just follows the animation; Squeeze and Smoothing still work.

- 💡 If the modifier has a bake, the bake is still shown with Simulate off. To compare with the plain animation,
  hide the modifier in the viewport instead, or delete the bake.

#### Zone
**Default** `0.333` · **Range** 0 – 1

Vertex group of the part that jiggles. Weight 1 is fully soft, weight 0 follows the animation rigidly and nothing
moves it. Softness is multiplied by this weight.

- 💡 As a plain number it makes the whole mesh equally soft: fine for a jelly or a prop, not for a character.
- 💡 Weights between 0 and about 0.2 are almost rigid. Use the range 0.3 – 1 for the visible jiggle and fade to 0
  at the border.
- 💡 For several parts with their own settings, use one Wiggy modifier per part ([4](#s4)).

#### Edge Fade
**Default** `0` · **Range** 0 – 64

Fades the zone out towards its border over this many rings of points, so a group assigned in Edit Mode (1 inside,
0 outside) blends into the body without a crease along its edge.

- 💡 0 uses the weights as they are: best for painted weights.
- 💡 3 – 4 is a good start for an Edit Mode group. In the demo the crease at the border went from 26 degrees to
  under 3.

#### Influence
**Default** `1` · **Range** 0 – 1

How much of the simulation is shown: 1 is the full simulation, 0 the plain animation. Good for fading the jiggle
in or out with keys.

<a id="s2-2"></a>
### 2.2 Dynamics

| Input | Default | Range |
|---|---|---|
| Softness | `0.2` | 0 – 1 |
| Mass | `6 kg` | — |
| Mass by Area | on | — |
| Damping | `1` | 0 – 100 |
| Wobble | `1` (off) | 0 – 1 |
| Max Distance | `0` (off) | m |

#### Softness
**Default** `0.2` · **Range** 0 – 1

How freely the zone moves away from the animation. Multiplied by the zone weight. **The main setting: start
here.**

- 💡 The response is steep. In the demo, 0.12 / 0.22 / 0.32 give a swing of 7 / 45 / 72 mm. Change it in steps
  of 0.02 – 0.05.
- 💡 0.15 – 0.25 for flesh on a character, 0.3 – 0.5 for very soft objects. Above 0.5 the zone barely follows the
  animation.

#### Mass
**Default** `6 kg`

Mass of a point of the zone (with Mass by Area, of an average one). Heavier means more inertia: the motion
overshoots further and swings longer.

- 💡 Mass and Softness work together: more mass with the same Softness is a slower, heavier, larger swing.

#### Mass by Area
**Default** on

Spreads the mass of the zone by surface area instead of evenly per point, so dense parts of the mesh are not
heavier. It is measured within the zone: an average point of the zone weighs Mass, and no point weighs less than
a quarter or more than twice that, so very dense and very coarse parts side by side do not tear the skin apart.

- 💡 Keep it on when the mesh density varies (a detailed nipple or face on a coarser body), otherwise dense areas
  stretch, pinch and crease.

#### Damping
**Default** `1` · **Range** 0 – 100

Slows down all motion of the zone, like moving in water. Higher calms down faster, but also drags the zone behind
fast animation.

- 💡 Values up to 1 are barely visible. Big values (8+) make the zone lag: in the demo Damping 8 lags 14 mm behind
  the motion.
- 💡 To calm the jiggle, use Wobble instead: it does not cause lag.

#### Wobble
**Default** `1` (off) · **Range** 0 – 1

How springy the zone is after a push, a hit or a sudden stop. 1 keeps every wobble; lower settles the jiggle
sooner **WITHOUT** dragging the zone behind the animation; 0 brings it back with no wobble at all. It damps only
the motion relative to the animation.

- 💡 At 0.5 the wobble speed halves in 0.1 s.
- 💡 0.6 – 0.8 dense, heavy flesh; 0.3 – 0.5 almost no leftover trembling; 1 cartoon jelly.
- 💡 Wobble also lowers the lag: on the test sphere 0.26 m (off) → 0.23 m (0.5) → 0.14 m (0).

#### Max Distance
**Default** `0` (off) · **Unit** m

The furthest a point may move away from its animated position. Keeps the flesh on the body in very hard moves and
jumps in the animation (a cut, a teleport, a 180-degree turn).

- 💡 0.1 – 0.2 m suits a human-sized character.
- 💡 The limit is smooth, not a wall: in the demo a water balloon swings 25 mm with 0.02 instead of 47 mm without
  it.
- 💡 Colliders and Self Collision may still push further.

<a id="s2-3"></a>
### 2.3 Shape

| Input | Default | Range |
|---|---|---|
| Stretching | `0.1` | 0 – 1 |
| Bending | `0.2` | 0 – 1 |
| Preserve Volume | `0` (off) | 0 – 1 |
| Sway | `0.8` | 0 – 1 |

#### Stretching
**Default** `0.1` · **Range** 0 – 1

How much the surface may stretch. Higher is more elastic.

- 💡 0.05 – 0.1 for skin. Very low (0.02) keeps lengths, e.g. for antennae or strands. High values with Preserve
  Volume make the volume swing more easily; stay below about 0.3.

#### Bending
**Default** `0.2` · **Range** 0 – 1

How easily the surface bends and shears. Higher creases more easily.

- 💡 0.05 – 0.08 keeps a jelly or a belly smooth; 0.2 for breasts; 0.3 for floppy parts.
- 💡 A belly with 0.15 rippled along the abs: use about 0.06.
- 💡 Skin that Grip drags along a collider wrinkles with 0.2: use 0.06.

#### Preserve Volume
**Default** `0` (off) · **Range** 0 – 1

How strongly every soft part keeps its volume. Pressed flesh bulges out around the contact instead of going flat
like a balloon.

- 💡 Each soft island keeps its own volume: two breasts in one zone are kept separately.
- 💡 It acts like incompressible flesh: pressed here, it bulges there. Jiggle and sway do not change the volume
  and stay as they are.
- 💡 0.5 – 1 for flesh and soft objects. In the demo a pressed cushion bulges at the sides 12.7 mm with 1, 0.7 mm
  without.
- 💡 It replaces "pressure" or "internal springs" of Cloth: you do not need anything else for a firm, elastic soft
  body.
- 💡 Under a hard press the volume can dip to 75 – 90 % for a moment and then comes back.

#### Sway
**Default** `0.8` · **Range** 0 – 1

How much every soft part moves as one heavy mass that keeps its shape: it swings about its base instead of
rippling point by point. Higher looks heavier and firmer. 0 is off.

- 💡 The default 0.8 is made for heavy flesh. For a springy jelly, a prop, a toy or grass use 0: with 0.8 such
  objects lag 30 – 40 mm and hardly swing after a stop.
- 💡 For a zone across a joint (buttocks + thighs, a whole leg) use 0 – 0.3: such a zone is one island, and moving
  it as one mass while the legs move apart creases the skin.
- 💡 With gravity keep it at 0.8 or below: at 1.0 a belly slowly sagged 13 mm in 6 s (1 mm at 0.8).
- 💡 In the demo, Sway 0 vs 0.9: 12 vs 34 mm of swing of a bobble.

<a id="s2-4"></a>
### 2.4 Gravity <sub>(panel toggle)</sub>

| Input | Default | Range |
|---|---|---|
| Gravity | off | — |
| Acceleration | `(0, 0, -9.81)` | m/s² |

#### Gravity
**Default** off

Pulls the zone down. Off, the jiggle keeps the animated shape (the sculpted shape is the rest shape); on, the soft
parts sag.

- 💡 The simulation starts from the unsagged shape, so it settles in the first frames. Start the animation a few
  frames after the start frame, or bake from an earlier frame.

#### Acceleration
**Default** `(0, 0, -9.81)` · **Unit** m/s²

Direction and strength of gravity, in world space.

- 💡 Full −9.81 on a soft zone needs firmer Softness (0.15 – 0.2), or the flesh sags a lot and falls into creases.
- 💡 A lying or sitting character often looks best with a weaker value (−0.5 to −3): weight without stretching
  the skin.
- 💡 Any direction works: sideways for a body on its side, etc.

<a id="s2-5"></a>
### 2.5 Collisions

| Input | Default | Range |
|---|---|---|
| Colliders | (none) | collection |
| Thickness | `0.01 m` | 0 – 1 |
| Friction | `0` | 0 – 1 |
| Push Aside | `0` (off) | 0 – 1 m |
| Cling | `0` (off) | 0 – 0.5 m |
| Grip | `0` (off) | 0 – 1 |

#### Colliders
**Default** (none) · **Type** collection

Collection of the objects the zone collides with, and of force fields. See [3](#s3) for how to set up a collider.

#### Thickness
**Default** `0.01 m` · **Range** 0 – 1

Contact thickness of the zone's points: the gap kept from colliders.

- 💡 Raise it a little if a thin collider pokes between the vertices.

#### Friction
**Default** `0` · **Range** 0 – 1

Friction against the colliders.

> [!IMPORTANT]
> The next three shape the contact. They are made for **SOFT OBJECTS** that a solid goes into or through (a rod
> through a soft ring, a finger into a pad). On a character pressed by hands leave them at 0: there they made the
> contact worse in tests. All three act in every substep.

#### Push Aside
**Default** `0` (off) · **Range** 0 – 1 m

Where a collider pushes into the zone, the skin around moves aside with it over this distance, so the solid opens
a wide, smooth way instead of a pit.

- 💡 0.05 – 0.08 in the demos.
- 💡 It moves skin that is already inside the collider. Under a flat end wider than a hole the nearest surface is
  that end, so the skin is pushed down, not aside: round the end or open the hole by bones.

#### Cling
**Default** `0` (off) · **Range** 0 – 0.5 m

Skin closer to a collider than this, beyond the contact, is drawn onto its surface, so the zone clings to it and
wraps around it. The pull fades towards the edge of the band.

- 💡 0.02 – 0.03 in the demos.

#### Grip
**Default** `0` (off) · **Range** 0 – 1

How strongly skin touching a collider is carried along with its motion: the touching skin follows the collider,
the rest of the zone follows with inertia and a little lag. 1 sticks.

- 💡 At 0.5 the sliding of the skin over the collider halves in 0.1 s. 0.2 is a weak pull, 0.8 a strong one (and
  more stretching).
- 💡 If the dragged skin wrinkles, lower Bending (about 0.06).

<a id="s2-6"></a>
### 2.6 Self Collision <sub>(panel toggle)</sub>

| Input | Default | Range |
|---|---|---|
| Self Collision | off | — |
| Collide With | Whole Mesh | menu |
| Collider Group | `1` | vertex group |
| Gap | `0.004 m` | 0 – 0.1 |
| Friction | `0.1` | 0 – 1 |
| *Advanced ›* Push Speed | `0.25 m/s` | 0 – 100 |
| *Advanced ›* Max Depth | `0.15 m` | 0 – 2 |
| *Advanced ›* Pose Steps | `20` | 0 – 500 |

#### Self Collision
**Default** off

The zone collides with its own mesh: the soft parts with each other and with the rigid parts of the body (arms,
the other breast, thighs).

- 💡 Needs the object option **"Add Rest Position"** (*Object Data properties, Shape Keys panel*); turn it on for
  every object that uses Self Collision. Overlaps that already exist in the rest pose (a nipple modelled into the
  areola) are ignored thanks to it.
- 💡 It is the slowest feature: about 1 s per frame on a 15 000 vertex character. Bake the result. When it is off
  it costs nothing.
- 💡 In the demo two lobes pressed together overlap 71 mm without it and 32 mm with it.

#### Collide With
**Default** Whole Mesh

What the zone collides with.

| Mode | Collides with |
|---|---|
| **Whole Mesh** | the soft parts with each other and with every rigid part of the mesh |
| **Group** | only the Collider Group, for example the hands |
| **Group + Zone** | the Collider Group, and the soft parts of the zone with each other |

#### Collider Group
**Default** `1` · **Type** vertex group

Vertex group of the part the zone collides with in the Group modes, for example only the hands (weight above 0.5).

#### Gap
**Default** `0.004 m` · **Range** 0 – 0.1

Gap kept between touching parts of the mesh.

#### Friction <sub>(Self Collision)</sub>
**Default** `0.1` · **Range** 0 – 1

How much touching parts of the mesh grip each other.

#### Advanced (sub-panel)

##### Push Speed
**Default** `0.25 m/s` · **Range** 0 – 100

How fast overlaps open up. Lower parts the skin more gently and keeps dense areas smooth; raise it only if overlaps
open too slowly.

- 💡 0.25 works well on characters. 0.5 gave a small buzz in the cleavage at rest, 1.0 more.
- 💡 It runs in simulation time, so Speed ([2.9](#s2-9)) scales it too.

##### Max Depth
**Default** `0.15 m` · **Range** 0 – 2

Deepest overlap that is still pushed apart. Raise it if parts of the model go deep into each other in the
animation.

- 💡 On a character with hands on the breasts 0.08 worked best.

##### Pose Steps
**Default** `20` · **Range** 0 – 500

In how many small steps the overlaps already present in the animated pose are pushed apart every frame. More gives
a smoother dent.

> [!TIP]
> **Tips for Self Collision**
> - Turn on Smoothing ([2.8](#s2-8)) with about 2 passes: without it, the cleavage can show spikes in hard presses.
> - Points at the border of the zone with a tiny weight (near-rigid) are barely pushed; this stops them from
>   flickering.
> - Fingertips thinner than the spacing of the skin's vertices can poke between them (only soft points are
>   tested).

<a id="s2-7"></a>
### 2.7 Squeeze <sub>(panel toggle)</sub>

| Input | Default | Range |
|---|---|---|
| Squeeze | off | — |
| Area | `0` | vertex group |
| Strength | `1` | 0 – 10 |
| Depth | `1` | 0 – 10 |

#### Squeeze
**Default** off

Squeezes the painted area before the simulation: where soft skin presses into the rest of the body (a breast
against the arm, a belly against the thighs), it is flattened against it and the skin around bulges out, so the
area of the skin is kept. It is cheap and also works with Simulate off.

- 💡 It acts only where the skin is already inside another part of the same mesh. Cast along the normal, the ray
  must hit that part.
- 💡 It is not a collider: use Self Collision or Colliders for real contact. Squeeze is the quick "flesh flattened
  by pressure" look.

#### Area <sub>(Squeeze)</sub>
**Default** `0` · **Type** vertex group

What gets squeezed.

#### Strength <sub>(Squeeze)</sub>
**Default** `1` · **Range** 0 – 10

Overall strength of the squeeze.

#### Depth
**Default** `1` · **Range** 0 – 10

How deep the surface is pressed in.

<a id="s2-8"></a>
### 2.8 Smoothing <sub>(panel toggle)</sub>

| Input | Default | Range |
|---|---|---|
| Smoothing | off | — |
| Area | `0` | vertex group |
| Strength | `1` | 0 – 1 |
| Passes | `5` | 1+ |

#### Smoothing
**Default** off

Smooths the deformation after the simulation (a corrective smooth only where you paint it). Removes small creases
and spikes without blurring the sculpted shape.

#### Area <sub>(Smoothing)</sub>
**Default** `0` · **Type** vertex group

Where to smooth.

#### Strength <sub>(Smoothing)</sub>
**Default** `1` · **Range** 0 – 1

How much of the smoothing is applied.

#### Passes
**Default** `5` · **Range** 1+

Blur passes. More smooths over a larger area.

- 💡 2 passes are enough to calm Self Collision spikes.

<a id="s2-9"></a>
### 2.9 Solver

| Input | Default | Range |
|---|---|---|
| Substeps | `4` | 1+ |
| Iterations | `8` | 1+ |
| Speed | `1` | 0.01 – 10 |
| Simulation Space | (identity) | matrix |

#### Substeps
**Default** `4` · **Range** 1+

Solver steps per frame. More is more accurate in fast motion and slower to compute.

- 💡 The motion follows the steps per **SECOND**: 10 at 24 fps, 4 at 60 fps and 2 at 120 fps move alike (2 – 4 %
  apart in normal motion; in extremely violent shaking, around 10 g, still up to about 25 %). With the same 4
  substeps, 24 fps moves 15 – 20 % differently from 60 fps.
- 💡 Raise it for fast colliders: one that moves further than the body is thick within a step can pass through.
  At 20+ m/s, 4 substeps miss; 16 catch.

#### Iterations
**Default** `8` · **Range** 1+

Constraint iterations per substep. More makes the surface hold its Stretching and Bending more precisely.

#### Speed
**Default** `1` · **Range** 0.01 – 10

How fast the simulation time runs: below 1 a slow-motion jiggle for slowed-down animation, above 1 a quicker one.
1 is real time.

- 💡 Slow the animation down by the same amount. If only the animation is slowed (Speed 1), the jiggle is too fast
  for it; if only Speed is lowered, the zone lags far behind (demo C3 shows all three).

#### Simulation Space
**Default** (identity) · **Type** matrix

Transform from simulation space to world space. It is not shown in the modifier panel; leave it as it is unless
the rig is offset or scaled and you know you need it.

---

<a id="s3"></a>
## 3. Colliders and force fields

### Setting up a collider

1. Put the collider objects into a collection (it can be hidden from render, not excluded from the view layer).
2. Give every collider Blender's **"Collider"** Geometry Nodes modifier. It is a node-group asset that comes with
   Blender (catalog *Simulation*): in the *Add Modifier* menu, type "Collider" to find it.
3. Pick the collection in *Wiggy › Collisions › Colliders*.

### Rules

- A closed mesh is solid.
- An open surface (a plane, a floor) keeps the body on the side its normals point to. If the body ends up behind
  it, flip its normals.
- The Collider modifier's **"Boundary"** option makes a container (the body is kept **INSIDE** the mesh). Do not
  use it for a floor.
- Margin, Friction and softness of the collider itself are set on its own Collider modifier.
- Moving, rotating and deforming colliders all work.
- The legacy Collision physics modifier is **NOT** seen by this solver.
- Fast colliders need more Substeps ([2.9](#s2-9)).
- A clean collider mesh matters for Push Aside / Cling / Grip: a Bevel with inverted or zero-area faces gives
  wrong "inside" results (Wiggy guards against the worst of it).
- The body's own arms, hands and legs are best handled by Self Collision ([2.6](#s2-6)), not by separate collider
  copies.

### Force fields (wind, etc.)

> [!CAUTION]
> A classic Force Field object (an Empty with a field) is **NOT** seen by the Geometry Nodes solver.

A force field must be made with Geometry Nodes, with Blender's **"Custom Force"** and **"Set Effector"** node
assets, and put into the Colliders collection.

This file has one ready: the node group **"Wiggy Wind"**. To use it, copy the object **"B4 Wind Field Steady"**
(or **"… Gusts"**) from the demo into the Colliders collection of your zone. Its inputs:

| Input | Meaning |
|---|---|
| Direction | direction of the wind (the arrow shows it) |
| Strength | force in newtons per point (5 in the demo) |
| Gust | 0 = steady wind, 1 = fully gusty |
| Gust Size | size of the gusts in meters |
| Gust Speed | how fast the gusts travel |

---

<a id="s4"></a>
## 4. Several zones on one body

Breasts, belly, buttocks and thighs usually need different settings.

- Add one Wiggy modifier per zone, each with its own vertex group, all right after the Armature and before
  Corrective Smooth / Subdivision.
- A lower modifier sees the zones above it already simulated (one-way coupling): put the zones that drive others
  (the torso parts) first.
- For a new zone, duplicate the **FIRST**, clean modifier and change only what you need; duplicating a tuned one
  carries all its settings over.
- Every modifier has its own bake.
- Keep the zones from overlapping much; let them fade out at their borders (painted weights or Edge Fade).
- Name the groups so they never match a bone name, e.g. "Wiggy Belly".
- Example (4 zones on a 15 000 vertex character): about 2.2 s per frame to simulate, instant playback once baked.

---

<a id="s5"></a>
## 5. Baking, speed and frame rate

### Baking

- Bake in the modifier's panel: *Manage › Bake*. The *Custom Range* there sets the first and last frame. Bake
  Target **"Disk"** writes the frames to the `blendcache_<file name>` folder next to the .blend file.
- Baked frames are read directly and nothing else of the group is computed: playback is instant even for heavy
  scenes.
- While a bake exists, changing settings shows nothing new, and Simulate off still shows the bake. **Delete the
  bake to see your changes.**

### Speed

- Geometry Nodes physics runs on the CPU only.
- Rough costs: the test sphere is real-time; a 15 000 vertex character takes about 0.1 s per frame, about 1 s with
  Self Collision; this whole demo file about 0.24 s per frame.
- While tuning: lower Substeps, hide or exclude collections you do not need (the demo collections too), turn Self
  Collision on last.
- Subdivision goes **AFTER** Wiggy: simulate the low-resolution mesh.

### Frame rate

- Physics is stable at 24, 60 and 120 fps.
- Keep the substeps per second, not per frame, when you change the frame rate:

| Frame rate | Substeps |
|---|---|
| 24 fps | 10 |
| 60 fps | 4 |
| 120 fps | 2 |

### Size of the object

- XPBD stiffness is absolute: the same settings act differently on a 10 cm toy and a 2 m character. The defaults
  suit 1 – 2 m characters. For very small or very large objects retune Softness, Stretching and Bending (scale
  0.01 and 100 were tested: stable, but different).

---

<a id="s6"></a>
## 6. Tuning guide: tips from development

**Order of tuning that works:**

| Step | Step |
|---|---|
| 1. Zone weights (and Edge Fade) | 5. Gravity, if wanted |
| 2. Softness | 6. Colliders / Self Collision |
| 3. Mass | 7. Max Distance for hard moves |
| 4. Wobble, Sway, Preserve Volume | 8. Substeps last, then bake |

<a id="s6-1"></a>
### 6.1 Recipes <sub>(starting points, all tested in development)</sub>

| Use | Settings |
|---|---|
| **Heavy, dense breasts** (character) | Softness 0.15 – 0.2, Mass 4 – 6, Mass by Area on, Preserve Volume 0.7 – 1, Sway 0.6 – 0.8, Wobble 0.5 – 0.7, Max Distance 0.1 – 0.2.<br>With hands touching: Self Collision on, Push Speed 0.25, Max Depth 0.08, Smoothing on with 2 passes. |
| **Belly** | Bending about 0.06 (higher ripples along the abs), Sway 0.8 or less, Preserve Volume 0.5 – 1. |
| **Buttocks and thighs** (one zone across the hips) | Softness 0.15 – 0.2, Sway 0 – 0.3, Mass by Area on, Preserve Volume 0 – 0.5. Let the zone fade out before the knees. Tested on a character running with 80-degree leg swings and sitting on a partner's lap. |
| **Springy jelly, soft prop, toy** | Softness 0.2 – 0.3, Bending 0.05, Preserve Volume 0.5, Sway 0, Wobble 1 for cartoon bounce or 0.3 – 0.5 to settle fast. |
| **Water balloon** | Softness 0.15, Preserve Volume 1, Gravity on, Sway 0.8, Max Distance about 0.02. |
| **Cushion that is pressed** | Softness 0.2, Stretching 0.2, Preserve Volume 1. |
| **Grass, strands, plants in wind** | Softness 0.5, Bending 0.1, Stretching 0.05, Sway 0, rigid root (zone weight 0 at the base); "Wiggy Wind" Strength 5, Gust 0.85. |
| **Soft ring or tube** that a solid goes through | Softness 0.35, Bending 0.06, Preserve Volume 0.5, Substeps 8, Push Aside 0.08, Cling 0.02, Grip 0.5. |
| **Soft pad** pressed and rubbed by a finger | Softness 0.22, Bending 0.05, Sway 0, Preserve Volume 0.5, Push Aside 0.05, Cling 0.03, Grip 0.7. |
| **Jelly creature that hops** (two zones on one mesh) | **Body:** Softness 0.22, Preserve Volume 1, Wobble 0.7, Gravity on, Sway 0.3, Substeps 6, Bending 0.08, floor in Colliders.<br>**Antennae:** Softness 0.32, Bending 0.3, Stretching 0.02, Sway 0, Gravity on, Mass 2. |
| **Slow motion** | Slow the animation down (e.g. to 0.3x) and set Speed to the same 0.3. |

<a id="s6-2"></a>
### 6.2 Problem → fix

| Problem | Fix |
|---|---|
| The jiggle is sluggish, lags, hardly bounces | Lower Sway (0 – 0.3). Raise Softness a little. Do not use Damping. |
| It wobbles too long | Wobble 0.3 – 0.6. (Damping would also make it lag.) |
| It looks like a stiff block / does not move at all | Check the Zone group and its weights; raise Softness; check that Simulate is on and no old bake is shown. |
| Flesh flies off the body in fast moves, cuts or jumps | Max Distance 0.1 – 0.2 m. More Substeps for very fast moves. |
| A crease runs along the border of the zone | Edge Fade 3 – 4 (Edit Mode group), or paint softer weights. |
| The skin crumples like paper, points fly off around a collider | Sway 0 – 0.3 when the zone crosses a joint (buttocks + thighs). Mass by Area on. For a big, coarse, very soft zone lower Mass or Softness. |
| Dense areas (nipples, face details) pinch, stretch or crease | Mass by Area on. With Self Collision, Push Speed 0.25. |
| Pressed flesh goes flat like a balloon | Preserve Volume 0.5 – 1. |
| The surface ripples or wrinkles | Lower Bending (0.05 – 0.08). Smoothing with 2 – 5 passes. |
| Skin dragged by a collider (Grip) wrinkles | Bending about 0.06; lower Grip. |
| The soft part sags too much with gravity | Softness 0.15 – 0.2, Sway 0.8 or less, or a weaker Acceleration. |
| A collider passes through the body | Substeps 8 – 16; make sure it has the Collider modifier and is in the Colliders collection; check its mesh is closed and clean. |
| The body is pushed through a plane / floor | Flip the plane's normals towards the body. Boundary off. |
| A force field does nothing | Classic Force Field objects are not seen. Use a Geometry Nodes field ("Wiggy Wind", or Custom Force + Set Effector) in the Colliders collection. |
| Spikes or flicker where soft parts touch (Self Collision) | Smoothing on, 2 passes; Push Speed 0.25; keep near-rigid weights at the zone border low. |
| A small buzz at rest with Self Collision | Lower Push Speed (0.25 or less). |
| Overlaps open too slowly | Raise Push Speed a little, or Max Depth if the overlap is deep. |
| Nothing changes when I change settings | A bake exists: delete it (*Manage › Bake*). Remember the simulation starts at the start frame: go back to it and play. |
| Part of the mesh stays behind or tears away from the rig | A vertex group (the zone) has the same name as a bone, and the Armature modifier uses it as bone weights. Rename the group. |
| Different motion at another frame rate | Keep the substeps per second ([5](#s5)). |
| Slow motion looks wrong | Slow the animation and lower Speed together. |
| Same settings look different on another model | Different size or mesh density: retune Softness first ([5](#s5)). |

---

<a id="s7"></a>
## 7. Known limits

- A vertex group named like a bone breaks the Armature skinning of those points. Always give zone groups their
  own names.
- Fingertips thinner than the skin's vertex spacing can poke between the skin's vertices with Self Collision.
- Very hard motion (e.g. 30 % of the body height in 3 frames) can push soft parts deeper into the body than Self
  Collision opens per frame; Max Distance helps.
- Tight creases (under the breasts, the buttock fold) under full gravity can partly fold into themselves.
- A finger wider than a hollow soft pad, dragged along it, can leave a stable fold (about 25 mm, about 59 mm with
  Preserve Volume 0.7 and Sway 0.8). A straight press and release recovers fully. For such shots use Sway 0 and a
  narrower collider.
- A rod with a flat end wider than a hole drags a soft ring down before it slides in (the plain solver does the
  same). Round or cone the end, or open the hole with bones in time.
- Legacy Collision modifiers and Force Field objects are not seen ([3](#s3)).
- CPU only. Bake heavy scenes.

---

<sub>Wiggy 0.1 · © 2026 @VOZHD1122 & @RoboDrozd · MIT License with Commons Clause — see [README ENG.md](README%20ENG.md#s4).</sub>
