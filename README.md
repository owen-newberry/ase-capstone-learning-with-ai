# Learning with AI

**Author:** Owen Newberry  
**Course:** ASE 485 – Capstone Project

---

## Overview

This repository documents how I use AI as a learning and development aid for my capstone project. 

My individual contributions focus on building a modular detection pipeline, implementing non-intrusive overlay rendering, and optimizing performance for real-time game analysis.

AI is used as a support tool to help research computer vision techniques, evaluate architectural decisions, prototype detection algorithms, and debug performance bottlenecks before and during implementation.

The purpose of this repository is to demonstrate intentional, structured use of AI to support learning and development—not to replace independent problem solving.

---

## Topic 1: Real-Time Screen Capture and Computer Vision

### Why I Need to Learn This

Some programs needs to capture and analyze game screens in real-time without impacting game performance. This requires understanding GPU-accelerated capture methods, efficient image processing, and detection algorithms that can run at 15-30 FPS.

A robust capture and detection system is essential for:

- Detecting UI state changes (inventory open, stash open, etc.)
- Recognizing item icons and extracting text via OCR
- Maintaining low latency between game events and overlay updates

### What I Want to Learn

- **Capture Methods**: Windows Desktop Duplication API vs BitBlt, GPU vs CPU capture
- **Region of Interest (ROI)**: Efficient selective capture instead of full-screen
- **Template Matching**: OpenCV techniques for UI element detection
- **Feature Matching**: ORB/SIFT for item icon recognition
- **Change Detection**: Minimizing unnecessary processing when nothing changes

### How I Learn with AI

- Ask AI to explain trade-offs between capture methods for different use cases
- Use AI to help debug detection pipeline performance bottlenecks
- Research OpenCV techniques and evaluate which fits my performance targets
- Validate my detection approach against best practices in game overlay development

AI serves as a technical research partner, not a source of copy-paste code.

---

## Topic 2: OCR Integration and Text Extraction in Games

### Why I Need to Learn This

Extracting item names, quantities, and stats from game UI requires reliable OCR that works with stylized game fonts. Traditional OCR engines often struggle with game text due to visual effects, anti-aliasing, and non-standard fonts.

Accurate text extraction is critical for:

- Reading item names and stack counts
- Parsing crafting requirements and inventory values
- Providing accurate recommendations to the player

### What I Want to Learn

- **OCR Engines**: Tesseract vs EasyOCR vs PaddleOCR - trade-offs and performance
- **Preprocessing**: Image preprocessing techniques to improve OCR accuracy
- **Region Targeting**: Extracting text from specific UI regions efficiently
- **Error Handling**: Graceful degradation when OCR confidence is low
- **GPU Acceleration**: Using CUDA for faster text recognition

### How I Learn with AI

- Research which OCR engine performs best for game UI text recognition
- Brainstorm preprocessing pipelines (thresholding, contrast, scaling) for accuracy
- Use AI to troubleshoot OCR misreads and identify improvement strategies
- Evaluate performance vs accuracy trade-offs for real-time constraints

AI is used to accelerate research and validate technical decisions.

---

## Topic 3: Real-Time Rendering Pipeline Concepts

### Why I Need to Learn This

My capstone patterns — fixed-timestep simulation, motion authority, time-based effects — all exist to feed clean data into a rendering pipeline. To reason about whether those patterns are working correctly, I need to understand what the pipeline actually expects and how it processes what the game provides.

Understanding the rendering pipeline is essential for:

- Knowing what buffers (depth, motion vector, color) the upscaler requires and why
- Reasoning about when and how game state gets translated into GPU-visible data
- Identifying which architectural decisions in game code directly affect render output quality

### What I Want to Learn

- **Render Graph / Frame Graph**: How modern engines sequence rendering passes and manage resource dependencies
- **G-Buffer and Deferred Rendering**: What data is packed into which buffers and how it flows downstream
- **Camera Jitter**: Why TAA and temporal upscalers require sub-pixel camera offsets each frame and how to implement them correctly
- **TAA History Buffers**: How temporal history is stored, reprojected, and invalidated
- **Depth Buffer Usage**: How depth is used for reprojection and why depth precision matters for temporal stability

### How I Learn with AI

- Ask AI to explain pipeline stages from a software engineering perspective, not just graphics theory
- Use AI to trace a single frame through the pipeline and identify where each capstone pattern contributes data
- Research how UE5's TSR and DLSS consume engine-provided buffers to understand what "correct input" actually looks like
- Validate my understanding of jitter implementation against vendor documentation and engine source

AI is used to bridge the gap between graphics-facing documentation and software engineering decisions.

---
## Topic 4: Neural Rendering and AI-Assisted Graphics

### Why I Need to Learn This

My capstone research centers on how AI-driven GPU features are reshaping game software architecture. To make a credible argument about *why* these architectural patterns matter now — and why they didn't need to exist five years ago — I need a solid understanding of what neural rendering actually is: how machine learning models are embedded into rendering pipelines, what they expect from the game, and how their role is expanding with architectures like Blackwell.

Understanding neural rendering is essential for:

- Grounding the "why now" argument in the research paper with technical substance
- Explaining the relationship between upscaler quality and game-side software decisions
- Understanding what DLSS's neural network, FSR's temporal logic, and frame generation's optical flow model are actually doing with the inputs the game provides

### What I Want to Learn

- **Neural Upscaling**: How DLSS uses a convolutional neural network trained on high-resolution data to reconstruct detail from lower-resolution inputs
- **Optical Flow in Frame Generation**: How DLSS 3/4 and FSR 3 estimate motion between frames to synthesize entirely new intermediate frames
- **On-Device Inference**: How neural network inference runs on dedicated GPU hardware (Tensor Cores, NPUs) and what that means for performance budgets
- **Training vs. Inference**: The distinction between how these models are built and how they run at game time — and why that matters for understanding their constraints
- **AI-Assisted Animation and NPC Behavior**: Emerging uses of neural models beyond rendering, such as physics-based character animation and real-time behavioral inference

### How I Learn with AI

- Ask AI to explain neural network architectures (CNNs, transformers) in the context of image reconstruction, not abstract ML theory
- Use AI to work through NVIDIA's DLSS technical deep-dives and AMD's FSR documentation at a level deeper than marketing summaries
- Research how Tensor Core utilization and dedicated AI silicon changed what was feasible in real-time neural rendering across GPU generations
- Validate my understanding of how frame generation differs architecturally from upscaling, and what additional software constraints it introduces

AI is used to build technical depth in an area that bridges my software engineering research question and the hardware trends driving it.

---