# Notes: Real-Time Rendering Pipeline Concepts

**Topic:** Understanding the rendering pipeline from a software engineering perspective  
**Source:** AI-assisted research sessions  
**Relation to Capstone:** My patterns (fixed-timestep, motion authority, time-based effects) all exist to feed clean data into this pipeline. I need to understand what the pipeline does with that data to know whether my patterns are actually working.

---

## Why the Pipeline Matters for My Project

I came into this thinking the rendering pipeline was mostly a graphics concern — something the engine handles for me. What I learned is that the pipeline places very specific *demands* on game-side code, and most of the artifacts my capstone is trying to prevent (ghosting, smearing, stuttering) happen because game code fails to meet those demands. The pipeline isn't just downstream from my code — it's a contract my code has to satisfy every frame.

---

## The Frame Graph / Render Graph

Modern engines don't run rendering passes in an ad-hoc sequence. They use a **render graph** (or frame graph) — a declarative description of all the passes that need to run in a frame, the resources each pass reads and writes, and the dependencies between them.

The key idea: by declaring resource dependencies up front, the engine can:
- Automatically insert GPU memory barriers at the right points
- Cull passes that don't contribute to the final output
- Schedule work to overlap GPU and CPU execution where possible

From a software engineering angle, this is essentially a DAG (directed acyclic graph) where nodes are render passes and edges are resource dependencies. UE5's RDG (Render Dependency Graph) works this way. The engine builds the graph each frame, compiles it, and then executes it.

**Why this matters for my capstone:** Motion vectors are written by a pass in this graph. If game code updates transforms *after* the motion vector pass has already run, the upscaler gets stale data. The render graph makes the ordering explicit — which means it also makes timing mistakes visible if you know where to look.

---

## G-Buffer and Deferred Rendering

Deferred rendering splits the work into two stages:

1. **Geometry Pass (G-Buffer fill):** Every visible surface writes its properties into a set of textures (the G-Buffer). Typical contents:
   - **Albedo/Base Color** — the raw surface color before lighting
   - **Normal** — surface orientation in world or view space
   - **Depth** — distance from the camera for each pixel
   - **Roughness/Metallic** — material properties for lighting
   - **Motion Vectors** — how far each pixel moved from the last frame

2. **Lighting Pass:** A full-screen pass reads from the G-Buffer and computes final lighting, without needing to re-process geometry.

The important insight for me: **motion vectors live in the G-Buffer.** They're a first-class output of the geometry pass, not an afterthought. Every object that moves needs to write a correct motion vector — a per-pixel record of its screen-space displacement since the last frame. The upscaler reads these vectors to reproject previous-frame data onto the current frame.

If an object's transform was modified by two different systems in the same frame (violating my Single-Writer Motion Authority pattern), the final transform used to render the object may not match the transform used to compute its motion vector. The rendered pixels end up in one place; the motion vector points somewhere else. That mismatch is exactly what causes ghosting.

---

## Camera Jitter

Temporal upscalers and TAA require the camera to be offset by a sub-pixel amount each frame in a repeating pattern. This is called **jitter**.

The reasoning: a 4K image has more pixels than a 1080p render. To reconstruct the missing detail, the upscaler accumulates samples from multiple frames. By shifting the camera slightly each frame, different frames capture slightly different sub-pixel detail. When the upscaler reprojects and blends those frames, it can infer detail that no single frame could resolve alone.

Jitter patterns are typically sequences of 2D offsets in [-0.5, 0.5] pixel range. Common sequences: Halton(2,3), which produces a low-discrepancy quasi-random pattern that covers sub-pixel space evenly over 8–16 frames.

**What game code has to do:**
- Apply the jitter offset to the projection matrix before rendering
- Remove the jitter offset when computing motion vectors (motion vectors should reflect actual object motion, not camera jitter)
- Pass the current frame's jitter offset to the upscaler as an explicit input

If game code forgets to unjitter the motion vectors, the upscaler sees phantom motion every frame and produces flicker. This is a concrete example of why understanding the pipeline matters — the requirement is buried in SDK documentation and only makes sense once you understand what the upscaler is doing with the data.

---

## TAA History Buffers

Temporal antialiasing and temporal upscaling both maintain a **history buffer** — a reprojected copy of the previous frame's output that the current frame blends with.

Each frame:
1. The current frame is rendered at low/native resolution
2. Motion vectors reproject the history buffer to align it with the current frame
3. The upscaler blends current frame data with the reprojected history
4. The result becomes the next frame's history

The history must be **invalidated** when it can't be trusted:
- Scene cuts / camera teleports
- Objects appearing or disappearing (disocclusion)
- Particles, transparency, and UI elements that move inconsistently

Engines manage this through a **reactive mask** — a per-pixel flag that tells the upscaler to reduce history weight for certain pixels. FSR2 calls this the "reactive mask"; DLSS has an "exposure" and "bias" mask. Game code is responsible for writing these masks for particles, animated textures, and transparent objects that wouldn't produce reliable motion vectors.

**Connection to my capstone:** If simulation state is non-deterministic frame-to-frame (breaking my Fixed-Timestep pattern), objects can jump in ways that make the history buffer invalid without the upscaler knowing. The result is ghosting — the history shows the object where it was, the current frame shows it somewhere else, and the blend produces a smeared trail.

---

## Depth Buffer

The depth buffer stores, for each pixel, a value representing distance from the camera. Depending on the API and configuration, depth is stored in several formats:
- **Linear depth**: actual distance in world units — easy to reason about, rarely used directly
- **Non-linear (perspective) depth**: more precision near the camera, less far away — the default in D3D and Vulkan
- **Reversed-Z**: far plane maps to 0, near plane maps to 1 — improves floating-point precision for distant geometry

Temporal upscalers use depth to:
- Determine which history samples are occluded vs. visible in the current frame
- Drive disocclusion detection — if depth changes significantly at a pixel, the history there should be discarded
- Weight blending decisions between history and current frame

**Precision matters:** With standard Z (near=1, far=0 in floating point terms), most of the precision is wasted on distant geometry and the near region has poor separation. Reversed-Z dramatically improves this. UE5 uses reversed-Z by default. Getting depth precision wrong can cause flickering at depth boundaries — another class of temporal artifact.

---

## How a Single Frame Actually Flows

Putting it together, a simplified frame looks like this:

1. **Simulation tick** (fixed timestep) → updates object positions, velocities, AI state
2. **State interpolation** → blends between last two simulation states for render-time position
3. **Render graph construction** → engine declares all passes and their resource dependencies
4. **G-Buffer pass** → geometry renders, writes albedo, normals, depth, **motion vectors**
5. **Lighting pass** → reads G-Buffer, produces lit image at render resolution
6. **Upscaler pass** → takes lit image + depth + motion vectors + jitter → outputs high-res image
7. **Post-processing** → bloom, tone mapping, UI (rendered *after* upscaling, directly at display resolution)
8. **Frame present** → final image to display

Every capstone pattern I'm implementing contributes to a specific step in this sequence. Fixed-timestep ensures step 1 is stable. Single-Writer Motion Authority ensures step 4 produces correct motion vectors. Time-Based Effects ensures animations in step 2 produce smooth, frame-rate-independent interpolated positions. Workload Budgeting ensures step 1 completes within its time budget so the rest of the frame isn't delayed.

---

## Key Takeaways

- The rendering pipeline is a **contract**: game code has to satisfy its requirements each frame or temporal reconstruction breaks
- Motion vectors are written to the G-Buffer and are the single most important game-side output for upscaler quality
- Camera jitter is applied to the projection matrix but must be *removed* from motion vector calculations
- History buffers depend on temporal consistency — non-deterministic simulation contaminates them
- Depth precision affects how well the upscaler handles depth discontinuities and disocclusion
- Understanding this pipeline is what makes my capstone patterns make sense — they're not arbitrary best practices, they're direct responses to what this pipeline requires
