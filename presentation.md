---
marp: true
theme: default
paginate: true
---

# Learning with AI
## Owen Newberry

---

# My Topics

1. Real-Time Screen Capture & Computer Vision
2. OCR Integration & Text Extraction in Games
3. Real-Time Rendering Pipeline Concepts
4. Neural Rendering & AI-Assisted Graphics

---

# Models Used

- Claude Sonnet 4.6
- ChatGPT

---

# Topic 1: Real-Time Screen Capture & Computer Vision

- Needed to capture and analyze live game screens at **15–30 FPS** without impacting game performance
- Evaluated **Windows Desktop Duplication API** vs **BitBlt** for GPU-accelerated capture
- Used **OpenCV** for template matching and region-of-interest (ROI) selection to minimize processing cost
- Learned change detection strategies to skip frames when nothing relevant changes

---

# Topic 1: Findings

- **Desktop Duplication API** is the clear winner over BitBlt — better performance and compatibility with modern Windows, but requires careful resource management
- **Multi-threaded architecture** (separate capture, processing, and render threads) is essential; thread-safe queues prevent bottlenecks
- GPU-based capture is faster and less intrusive but adds setup complexity — worth it at 15–30 FPS targets
- Resolution and DPI must be detected dynamically; games can alt-tab, minimize, or change resolution mid-session and the pipeline must recover gracefully

---

# Topic 2: OCR Integration & Text Extraction in Games

- Game UI uses stylized fonts — standard OCR struggles with anti-aliasing and visual effects
- Compared **Tesseract**, **EasyOCR**, and **PaddleOCR** for accuracy and real-time performance
- Applied preprocessing pipelines (thresholding, contrast enhancement, scaling) to improve read accuracy
- Built graceful degradation logic for low-confidence reads to avoid bad recommendations

---

# Topic 2: Findings

- **Preprocessing is non-negotiable** — adaptive thresholding, erosion/dilation, and color channel isolation each improved accuracy; combining them required per-element tuning
- Tesseract performs better when restricted to expected characters (uppercase, digits) and trained on game-specific font data — generic models produce too many errors on stylized UI
- **Bounding box validation** (filtering detections by UI region and expected dimensions) was the most effective false-positive reduction technique
- Animated or flashing UI elements required temporal frame averaging to stabilize OCR input before any engine could reliably read them



