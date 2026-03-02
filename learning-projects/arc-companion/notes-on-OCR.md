# Notes on OCR Integration

## Purpose
The goal of integrating OCR (Optical Character Recognition) in the Arc Companion project is to extract text information from the Arc Raiders game UI, specifically to read inventory item names, quantities, and other relevant stats. This enables the overlay to keep track of the player's inventory even when the in-game inventory view is limited or closed.

## Key Concepts Learned
- **Font and UI Analysis:** Game UIs often use custom fonts, outlines, shadows, and anti-aliasing. Understanding how these affect OCR accuracy is crucial. Research suggests that binarization and edge detection can help isolate text from backgrounds.
- **Image Preprocessing Techniques:** Explored adaptive thresholding, morphological operations (erosion/dilation), and color channel isolation. Learned that combining multiple preprocessing steps can significantly boost OCR results, but may require tuning for each game or even each UI element.
- **OCR Engine Configuration:** Tesseract supports custom language data and character whitelists/blacklists. Training Tesseract on game-specific fonts or restricting recognition to expected characters (e.g., uppercase, numbers) can reduce errors.
- **Bounding Box Extraction:** Some OCR engines provide bounding boxes for detected text. This can be used to filter out false positives by checking if the box is in a plausible UI region or matches expected dimensions.
- **Batch Processing and Parallelization:** For real-time overlays, batching OCR requests or running them in parallel (multi-threading or async) can help maintain performance.

## What I've Explored

## Practical Tips & Insights
- **Lighting and Color Consistency:** Inconsistent lighting or color changes (e.g., in-game effects) can reduce OCR accuracy. Consider normalizing brightness/contrast or using color masks.
- **UI Animation Handling:** Animated UI elements (e.g., flashing or moving text) may require frame averaging or temporal filtering to stabilize OCR input.
- **Debugging OCR Output:** Visualizing OCR bounding boxes and confidence scores overlaid on the game screen helps quickly identify problem areas.
- **False Positive Reduction:** Use domain knowledge (e.g., known item names, expected inventory layout) to validate or autocorrect OCR output.

## Challenges

## Research Questions for Future Work
- How can deep learning-based OCR (e.g., CRNN, transformer models) improve accuracy for stylized game text?
- What are the best practices for training custom OCR models on small datasets (e.g., screenshots from a single game)?
- Can OCR be combined with template/icon detection to further reduce errors?

