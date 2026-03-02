# Notes on Real-Time Screen Capture (RTSC)

## Purpose
Real-Time Screen Capture (RTSC) is a core component of the Arc Companion project. The goal is to capture the game screen efficiently and reliably, enabling the overlay to detect when the inventory is open and to extract relevant UI information for further processing (like OCR).

## Key Concepts Learned
- **Screen Capture APIs:** Learned the differences between BitBlt, Windows Desktop Duplication API, and newer GPU-accelerated methods. Desktop Duplication is preferred for performance and compatibility with modern Windows versions, but requires careful resource management.
- **Frame Rate and Latency:** High capture rates (15-30 FPS) are needed for responsive overlays. Research shows that minimizing frame copy and conversion steps is critical for low latency.
- **Multi-Threaded Architecture:** Separating capture, processing, and rendering into different threads prevents bottlenecks and keeps the overlay responsive. Thread-safe queues or buffers are often used to pass frames between stages.
- **Resolution and DPI Handling:** Games may run at different resolutions or DPI settings. Dynamic detection and scaling of capture regions is necessary for robustness.
- **Performance Profiling:** Tools like Windows Performance Analyzer or built-in profilers help identify bottlenecks in the capture pipeline.

## What I've Explored

## Practical Tips & Insights
- **GPU vs CPU Capture:** GPU-based capture is generally faster and less intrusive, but may require more complex setup and permissions.
- **Synchronization:** Ensuring that capture and processing threads are properly synchronized prevents dropped or duplicated frames.
- **Error Handling:** Games may minimize, alt-tab, or change resolution. The capture pipeline should detect and gracefully handle these events.
- **Testing Across Setups:** Testing on different hardware, monitors, and game settings is important to ensure reliability.

## Challenges

## Research Questions for Future Work
- How can DirectX or Vulkan-based capture improve performance for modern games?
- What are the best strategies for minimizing CPU/GPU usage while maintaining high capture quality?
- Can hardware overlays or dedicated capture cards offer advantages for certain use cases?
