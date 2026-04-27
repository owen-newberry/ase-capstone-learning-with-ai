---
marp: true
theme: default
paginate: true
---

# Neural Rendering & AI-Assisted Graphics

**Owen Newberry** · Learning with AI · Capstone support topic

---

## What “neural rendering” means here

**Any** stage of image production that uses a **trained neural network** — not an AI-generated whole frame, but e.g. **DLSS** replacing a hand-written upscaler with a CNN.

**Capstone link:** New GPU features changed what *game code* must deliver (clean motion, stable history, pacing).

---

## DLSS-style neural upscaling (concept)

**Inputs** (typical): low-res color, **motion vectors**, **depth**, exposure, **jitter**, reprojected **history**  
**Output:** high-res image  

Architecture often **U-Net**-like: encode → decode with skip connections; runs on **Tensor Cores** as compute.

**Trained** offline on high-quality ground truth; at runtime only **inference** — weights fixed.

---

## Why my capstone cares

The network was trained on **well-behaved** inputs. **No** “this motion vector is garbage” flag — bad inputs still get a confident-looking wrong output.

**The lever developers have** is **input quality** (and masks), not retraining the model.

---

## DLSS family (high level)

- **1.0** — per-game, weaker  
- **2+** — temporal, generalized network, motion + history (the pattern that “stuck”)  
- **3+** — **frame generation** added (separate problem from upscaling)  
- **4 / MFG** — more synthetic frames; stricter **latency** and **pacing** demands  

---

## Frame generation vs upscaling

| | Upscaling | Frame generation (e.g. DLSS 3+) |
|---|---|---|
| **Idea** | 1 low-res → 1 high-res | Synthesize **between** two rendered frames |
| **Extra** | Motion, depth, history | Often **optical flow** on GPU (e.g. OFA) — not the same as game motion vectors |
| **Constraint** | Stable vectors & history | **Frame pacing**, CPU not starved, UI after pass or it **smears** |

---

## FSR (contrast)

- **FSR 1** — spatial, no temporal NN  
- **FSR 2/3** — temporal, **hand-written** algorithm, still needs motion, depth, jitter, masks  
- **FSR 4** — adds neural component — closer to DLSS architecturally  
Open docs help trace **what** the stack expects from game code.

---

## On-device inference

Inference is **matmul-heavy** — **Tensor Cores**, often **FP16/INT8** (sometimes FP4 on new hardware). Often **memory-bandwidth** limited.

**Training** = offline, data center; **inference** = what games ship — that’s where architectural constraints live.

---

## “Why now” — hardware (e.g. Blackwell)

More die area to **AI**, better **OFA**, **FP4** for huge nets — enables **multi–frame gen** and heavier models at real-time budgets.

**Story:** new silicon → new features → **tighter** software contracts — matches the capstone’s design-pattern argument.

---

## Extras (forward-looking)

Neural **animation** / light **behavior** stacks add more GPU work beside rendering + upscaling → **workload budgeting** remains relevant.

---

## Takeaways

- Neural upscalers are **black-box inference**; fix artifacts by **fixing inputs** and masks.  
- **Frame generation** adds **pacing** and **latency** issues on top of upscaling.  
