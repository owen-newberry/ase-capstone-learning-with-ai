---
marp: true
theme: default
paginate: true
---

# Learning with AI
## Owen Newberry

---

# What I Researched

1. Real-Time Rendering Pipeline Concepts
2. Neural Rendering & AI-Assisted Graphics

AI Tools: **Claude Sonnet 4.6**, **ChatGPT**

---

# Topic 1: The Rendering Pipeline

**Why I needed this:**
- My capstone involves patterns that ultimately feed clean data into the rendering pipeline
- I needed to understand more in depth the parts of the rendering pipeline and its requirements

---

## The Pipeline Is a Contract

Every frame, game code must deliver:
- **Motion vectors** — per-pixel displacement since last frame
- **Consistent timing** — frames arrive at predictable intervals
- **Stable simulation state** — no mid-frame transform changes

**Break the contract →** ghosting, smearing, flickering

---

## Motion Vectors

**What they are:**
- Per-pixel 2D vector: "this pixel moved (dx, dy) since last frame"
- Written during geometry pass, stored in G-Buffer

**What they're used for:**
- **Reprojection** — upscaler shifts last frame's output to align with current frame
- Enables temporal accumulation of detail across frames

**What breaks them:**
- Multiple systems moving same object in one frame
- Motion vector computed from wrong transform → ghosting

---

## Other Requirements

**Camera Jitter**
- Sub-pixel camera offsets each frame (Halton sequence)
- Accumulates detail across frames for upscaling
- Must remove jitter from motion vectors or you get flicker

**History Buffers**
- Upscaler keeps reprojected copy of previous output
- Must invalidate on: camera cuts, teleports, disocclusion

**Depth Buffer**
- Used for occlusion detection, blend weighting
- Reversed-Z improves precision at distance

---

## Key Takeaway

- Pipeline **requires** specific data in specific formats at specific times
- Miss the mark → visible artifacts, not just "suboptimal"

---

# Topic 2: Neural Rendering

- Neural upscalers (DLSS 4, FSR 3) created new constraints
- These constraints didn't exist 5 years ago
- Clean software design is even more important with the increased complexity of the algorithms

---

## What DLSS Does

**Input (every frame):**
- Low-res rendered image
- Motion vectors (per-pixel)
- Depth buffer
- Camera jitter offset
- Previous frame's high-res output (history)

**Output:**
- High-res image with inferred detail from training + temporal history

---

## Why Clean Inputs Matter

**The key insight:**
- DLSS was trained on clean, well-behaved inputs
- Accurate motion vectors, stable history
- When inputs are noisy → artifacts, not graceful degradation

**No fallback:**
- Network can't detect "this input is wrong"
- Just applies learned patterns → wrong output

---

## Frame Generation (DLSS 3+)

**Different from upscaling:**
- Upscaling: 1 low-res frame → 1 high-res frame
- Frame gen: 2 rendered frames → synthesize intermediate frames

**How it works:**
- GPU optical flow analyzes pixel motion between frames
- Interpolates to create plausible in-between frame

**New constraints:**
- Frame pacing must be rock-solid
- UI must render after frame gen (or it smears)

---

## The Black Box Problem

**From a developer's perspective:**
- Neural network is a black box
- Can't tweak the network to fix artifacts
- Can only improve inputs

Input quality is the only lever developers have

---



