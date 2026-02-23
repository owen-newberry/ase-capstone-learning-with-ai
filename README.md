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

## Topic 3: Multi-Threaded Architecture for Real-Time Overlays

### Why I Need to Learn This

A responsive overlay requires careful thread management—capture, detection, and rendering must never block each other. Poor threading can cause stuttering, missed frames, or even game performance degradation.

The goal is to build a system that:

- Maintains consistent overlay updates without blocking the game
- Gracefully handles varying workloads (idle vs active gameplay)
- Remains stable and doesn't crash if one component fails

### What I Want to Learn

- **Thread Isolation**: Separating capture, detection, and rendering pipelines
- **Event-Driven Updates**: Only recomputing when relevant data changes
- **Thread-Safe State**: Managing shared data between threads without race conditions
- **Graceful Degradation**: Keeping the system functional if individual features fail
- **Performance Profiling**: Identifying and resolving threading bottlenecks

### How I Learn with AI

- Study multi-threaded architecture patterns for real-time applications
- Evaluate thread synchronization approaches (locks, queues, events)
- Use AI to review threading code for potential race conditions or deadlocks
- Research Python-specific concurrency patterns (threading, asyncio, multiprocessing)

AI is used to validate architectural decisions and identify potential issues early.

