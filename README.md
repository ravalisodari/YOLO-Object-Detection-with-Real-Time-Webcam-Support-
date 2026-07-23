# 🎯 YOLO Object Detection (with Real-Time Webcam Support)

Object detection using **Ultralytics YOLO**, run on static images and a live webcam feed. Includes an experimental, in-progress extension toward driver drowsiness detection (EAR/MAR-based) using MediaPipe.

---

## 📌 Overview

This project uses **YOLO (You Only Look Once)** — a fast, real-time object detection model — to identify and localize objects in:
- **Static images** (e.g., detecting "apple" in a sample photo)
- **Live webcam video** (e.g., detecting "person", "cell phone", "tie", "remote" in real time)

It also contains an **early-stage, not-yet-working** attempt at extending this into drowsiness detection using Eye Aspect Ratio (EAR) and Mouth Aspect Ratio (MAR) via MediaPipe's Face Landmarker — see [Status](#-status--known-issues) below.

---

## ✨ Features (Working)

- **Image detection** — run YOLO on a single image, view annotated results with bounding boxes and class labels
- **Real-time webcam detection** — live object detection loop using OpenCV + YOLO
- Uses lightweight **nano models** (`yolo26n.pt`, `yolov8n.pt`) for fast inference
- Auto-downloads pretrained YOLO weights on first run

---

## 🛠️ Tech Stack

| Component | Tool |
|---|---|
| Language | Python |
| Object Detection | [Ultralytics YOLO](https://github.com/ultralytics/ultralytics) |
| Image/Video Handling | OpenCV |
| (Planned) Face Landmarks | MediaPipe |

---

## 🚀 Getting Started

### 1. Install dependencies
```bash
pip install opencv-python ultralytics
```

### 2. Run image detection
```python
from ultralytics import YOLO
import cv2

model = YOLO("yolo26n.pt")  # nano model — fastest, least accurate
result = model(source="path/to/your/image.jpg")

cv2.imshow("Detection", result[0].plot())
cv2.waitKey(0)
cv2.destroyAllWindows()
```

### 3. Run real-time webcam detection
```python
import cv2
from ultralytics import YOLO

model = YOLO("yolov8n.pt")
cap = cv2.VideoCapture(0)

while True:
    ret, frame = cap.read()
    if not ret:
        break

    results = model(frame)
    annotated_frame = results[0].plot()

    cv2.imshow("Object Detection", annotated_frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

---

## 🧠 Model Size Reference

YOLO comes in multiple sizes — trade-off between speed and accuracy:

| Size | Name | Speed | Accuracy |
|---|---|---|---|
| Nano | `n` | Fastest | Lowest |
| Small | `s` | Fast | Low-Medium |
| Medium | `m` | Medium | Medium |
| Large | `l` | Slow | High |
| Extra Large | `x` | Slowest | Highest |

This notebook uses the **nano** models for fast, real-time performance.

---

## ⚠️ Status & Known Issues

This notebook is a **work in progress**. Be upfront about this when sharing/pushing it:

- ✅ **Image detection** — works as shown in the notebook output (correctly detected an apple in the sample image)
- ✅ **Webcam detection** — works, detects people/objects in real time
- ❌ **Drowsiness detection extension** — **currently broken**. The notebook attempts to add EAR/MAR-based drowsiness detection using MediaPipe, but the cell throws `NameError: name 'mp' is not defined` because:
  - `import mediapipe as mp` is missing
  - The `detector` object (MediaPipe Face Landmarker) is never created
  - `left_eye_aspect_ratio()`, `right_eye_aspect_ratio()`, and `mouth_aspect_ratio()` are called but never defined
  - `winsound.Beep()` is Windows-only and will fail on Mac/Linux without a fallback

**Before pushing to GitHub**, either:
1. Remove the broken drowsiness cell and keep this as a clean YOLO detection repo, or
2. Finish the implementation (define the missing functions, add the MediaPipe setup) so the notebook runs top-to-bottom without errors

---

## 📁 Project Structure
```
yolo-object-detection/
│
├── YOLO.ipynb              # Main notebook (image + webcam detection)
├── yolo26n.pt               # Auto-downloaded nano model weights
├── yolov8n.pt                # Auto-downloaded YOLOv8 nano weights
└── README.md
```

---

## 🗺️ Next Steps

- [ ] Fix or remove the incomplete drowsiness detection cell
- [ ] Add `requirements.txt` for reproducible installs
- [ ] Convert notebook cells into a proper `.py` script for production use
- [ ] Add sample images/output screenshots to the README
- [ ] Cross-platform alert sound (replace `winsound` with `playsound` or `pygame`)

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
