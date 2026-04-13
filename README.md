 Palm Detection Android App

An Android application that uses MediaPipe Hand Landmarker and OpenCV 
to detect, analyze, and capture palm and finger images in real time.

---

## Features

- Real-time palm detection using MediaPipe Hand Landmarker
- OpenCV-based frame preprocessing:
  - Blur detection (Laplacian variance)
  - Brightness enhancement (Gamma correction)
  - Contrast enhancement (CLAHE)
  - Edge sharpening (Unsharp masking)
- Live skeleton overlay drawn directly on enhanced frames
- Per-hand analysis:
  - Left/Right hand detection
  - Finger count (0–5) shown as x/5
  - Palm vs Back of hand orientation detection
  - Flip warning when back of hand detected
- Smart capture button — enabled only when:
  - Enhanced frame is showing
  - Hand is detected
  - Skeleton is visible
  - Finger count is ready
  - Palm is facing camera
- Saves to Gallery (Pictures/Finger Data/):
  - 1 clean palm image per hand (no skeleton)
  - 5 clean finger images per hand (no skeleton)
- Naming format:
  - Left_Hand_20260412_102530.png
  - Left_Hand_Thumb_Finger_20260412_102530.jpg
  - Left_Hand_Index_Finger_20260412_102530.jpg
  - Left_Hand_Middle_Finger_20260412_102530.jpg
  - Left_Hand_Ring_Finger_20260412_102530.jpg
  - Left_Hand_Little_Finger_20260412_102530.jpg
- Capture metadata saved to internal storage:
  - Device ID
  - Camera type (Rear/Front)
  - Focal length
  - Aperture
  - Focus distance (via Camera2)
  - Brightness score
  - Blur score
- Result screen after capture showing all scores

---

## Tech Stack

| Technology | Purpose |
|---|---|
| Kotlin | Primary language |
| Jetpack Compose | UI framework |
| CameraX | Camera feed + image analysis |
| Camera2 Interop | Focus distance metadata |
| MediaPipe Tasks Vision | Hand landmark detection |
| OpenCV 4.9.0 | Frame preprocessing |
| Compose Navigation | Screen navigation |
| AndroidViewModel | State management |
| MediaStore API | Gallery saving |
| Gson | Metadata JSON storage |

---
## Project Structure

```text
com.example.palmdetection
│
├── MainActivity.kt
│
├── presentation/
│   ├── BitmapOverlayHelper.kt
│   ├── CameraPreview.kt
│   ├── CameraScreen.kt
│   ├── CameraUiState.kt
│   ├── CameraViewModel.kt
│   ├── Navigation.kt
│   └── ResultScreen.kt
│
├── domain/
│   ├── HandAnalyzer.kt
│   ├── HandInfo.kt
│   └── CaptureResult.kt
│
├── data/
│   ├── CaptureManager.kt
│   ├── FramePreprocessor.kt
│   ├── HandLandmarkerHelper.kt
│   ├── MetadataManager.kt
│   └── CameraMetadata.kt
```

## Requirements

- Android Studio Hedgehog or later
- Android device or emulator with API 26+
- Physical device recommended for camera testing
- MediaPipe hand_landmarker.task model file

---
## Approach & Challenges

### Approach

**Frame Preprocessing Pipeline**
Every camera frame goes through a pipeline before
reaching the MediaPipe model:
Raw Frame (30fps)
↓
FPS Gate (80ms interval → ~12fps)
↓
Blur Check — Laplacian variance via OpenCV
↓
Brightness Check — HSV V-channel average
↓
Gamma Correction — if too dark
↓
CLAHE — local contrast enhancement
↓
Unsharp Mask — edge sharpening
↓
MediaPipe Model

### Challenges & Solutions

| Challenge | Solution |
|---|---|
| Skeleton misaligned with hand | Drew skeleton directly on MediaPipe input bitmap instead of screen overlay |
| Finger crops losing shape |
| Camera context leak in ViewModel | Switched to AndroidViewModel with applicationContext |
| Frame rate overwhelming model | FPS gate in FramePreprocessor limits to ~12fps to 30fps as per device specifications|
| Dark environment detection | Gamma correction LUT brightens frames before sending to model | Cross product of wrist vectors determines orientation |
| SDK version conflicts | Kept minSdk 26, removed setTargetFrameRate API 31 call |

---

## Permissions Required

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"
    android:maxSdkVersion="28" />
<uses-permission android:name="android.permission.READ_MEDIA_IMAGES" />
```

---

## Screenshots

| Right Palm detected | Captured Details|
|--------|----------|
| ![](Screenshots/Screenshot_20260412_211414_Palm%20Detection.jpg) | ![](Screenshots/Screenshot_20260412_211441_Palm%20Detection.jpg) |

| Left Palm detected | Capture Details|
|------------|--------|
| ![](Screenshots/Screenshot_20260412_211518_Palm%20Detection.jpg) | ![](Screenshots/Screenshot_20260412_211539_Palm%20Detection.jpg) |

| No Palm Detected | wrong orientation detected|
|-----------|-------|
| ![](Screenshots/Screenshot_20260412_211614_Palm%20Detection.jpg) | ![](Screenshots/Screenshot_20260412_211705_Palm%20Detection.jpg) |


---

## Author

Shivam Kumar
shivamkumar99852@gmail.com
