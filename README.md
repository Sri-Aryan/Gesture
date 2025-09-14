# Sign-Language Detection 

Android camera app for live **object detection** and **sign-language classification** using **TensorFlow Lite** + **OpenCV**.

---

## 🏗️ Overview
Captures camera frames with OpenCV, detects objects with a TensorFlow Lite SSD-style model, crops detections, and classifies each region to predict sign-language letters **A–Y**. Runs fully on-device with GPU acceleration for real-time results.

---

## 🛠️ Tech Stack
- **Language:** Java (Android SDK 34+)
- **Frameworks / Libraries:**
  - 🟢 [OpenCV 4.x](https://opencv.org/) – camera pipeline, image ops, overlays
  - 🔶 [TensorFlow Lite](https://www.tensorflow.org/lite) – on-device inference
  - ⚡ TensorFlow Lite **GPU Delegate** – hardware acceleration
- **Android Components:** `CameraBridgeViewBase`, `Activity`, `SurfaceView`
- **Build System:** Gradle

---

## 🧩 Architecture
- **CameraActivity**
  - Initializes OpenCV and requests camera permissions.
  - Streams frames via `CvCameraViewListener2`.
  - Sends each RGBA frame to `objectDetectorClass` and displays annotated output.
- **objectDetectorClass**
  - Loads **two TFLite models** (`hand_model.tflite` detection + `Sign_language_model.tflite` classification) and label map.
  - Converts `Mat` → `Bitmap` → `ByteBuffer`, normalizes pixels, runs inference.
  - Scales bounding boxes, draws rectangles and predicted labels.

---

## 🔄 Data Flow
`Camera Frame → Mat Rotation → Bitmap → ByteBuffer → 🧠 TFLite Detection → Cropped ROI → 🧠 TFLite Classification → Overlay → Display`

---

## ⚙️ Key Implementation Details
- **ByteBuffer Conversion:** Custom ARGB_8888 → float32 `[0,1]`.
- **GPU Delegate:** `GpuDelegate` for higher FPS.
- **Threading:** 4 threads for detection, 2 for classification.
- **Coordinate Scaling:** Boxes scaled back to original frame and clamped.
- **Dynamic Labeling:** Logits mapped to letters via `get_alphanets()`.

---

## 📋 Requirements
- Android device with **Camera2** support and **OpenGL ES 3.0+**.
- Place assets:
  - `hand_model.tflite`
  - `Sign_language_model.tflite`
  - `custom_label.txt`
- Add OpenCV SDK to `app/src/main/jniLibs`.

---

## 🚀 Build & Run
1. Copy the `.tflite` models and label file to `assets/`.
2. Add dependencies:
   ```gradle
   implementation 'org.opencv:opencv-android:4.x'
   implementation 'org.tensorflow:tensorflow-lite:2.x'
   implementation 'org.tensorflow:tensorflow-lite-gpu:2.x'
