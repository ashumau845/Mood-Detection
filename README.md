# Mood Detection (CameraX + ML Kit + TFLite) – Starter App

A minimal Android app that captures a selfie, detects the largest face with ML Kit, runs a TensorFlow Lite FER model on the face crop, and shows the predicted emotion and confidence—all on a single Compose screen.

This repo is a solid base if you plan to expand into a full Mood Detection Music App (Spotify/local playback, Room stats, charts, reminders).

## Features

- CameraX preview + photo capture (Compose UI)
- ML Kit Face Detection to crop the largest face
- TensorFlow Lite inference on a FER2013-style model
- Displays top-1 emotion with confidence
- Lightweight, single-screen sample ready to extend

## Tech Stack

- Kotlin + Jetpack Compose
- CameraX
- ML Kit Face Detection
- TensorFlow Lite (TFLite)
- Coroutines

## Quick Start

1) Create or open an empty Compose app in Android Studio.

2) Add dependencies (module `build.gradle` or `build.gradle.kts`):
dependencies {
  // Compose
  implementation(platform("androidx.compose:compose-bom:2024.08.00"))
  implementation("androidx.compose.material3:material3")
  implementation("androidx.activity:activity-compose:1.9.2")
  implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.8.4")
  implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.8.4")

  // CameraX
  implementation("androidx.camera:camera-core:1.3.4")
  implementation("androidx.camera:camera-camera2:1.3.4")
  implementation("androidx.camera:camera-lifecycle:1.3.4")
  implementation("androidx.camera:camera-view:1.3.4")

  // ML Kit Face Detection
  implementation("com.google.mlkit:face-detection:16.1.5")

  // TensorFlow Lite
  implementation("org.tensorflow:tensorflow-lite:2.12.0")
  implementation("org.tensorflow:tensorflow-lite-support:0.4.3")

  // Coroutines
  implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.8.1")
}

3. Add camera permission to AndroidManifest.xml:

<uses-permission android:name="android.permission.CAMERA" />

4. Put your model at:
app/src/main/assets/fer2013_model.tflite

5. Add the main screen. Replace the package name, then drop this file at:
app/src/main/java/your/package/MainActivity.kt

6. Use the provided MainActivity.kt from this project (the one with CameraX + ML Kit + TFLite and the dynamic EmotionClassifier).

Build & Run
Install on a device (recommended) or emulator with a camera.
Grant camera permission.
Tap “Capture & Detect”.
You’ll see a cropped face preview and the predicted emotion + confidence.

## How It Works
CameraX shows a preview. When you tap capture, it takes a high-quality still.
The image is rotated to upright and sent to ML Kit’s Face Detector.
The largest face is cropped from the original image.
The face crop is resized to the model’s expected input size and format.
The TFLite model runs inference and returns a class score per emotion.
The top-1 emotion and confidence are displayed in the UI.

## Model Notes
Default label order used in UI:
["angry","disgust","fear","happy","sad","surprise","neutral"]
If your model uses a different order or class set, update the labels accordingly.
Input shape can vary: many FER models are 48×48×1 (grayscale), some are 64×64×1, and others are RGB.
The included EmotionClassifier auto-detects input shape (H, W, C) and type (FLOAT32 or quantized) from the model’s tensor metadata and adapts preprocessing accordingly.



 
**Permissions**
CAMERA: required to capture a selfie.
No network permissions are required for this core sample.


## Troubleshooting
Error: “Cannot copy to a TensorFlowLite tensor … with 16384 bytes from a Java Buffer with 9216 bytes”

Your model expects a larger input (e.g., 64×64 vs 48×48). The provided dynamic EmotionClassifier fixes this by reading the input tensor shape and resizing accordingly. Make sure you’re using that version.
Black screen or no preview

Ensure camera permission is granted in system settings.
Close other apps that may be using the camera.
Face not detected

Ensure good lighting and your face is fully in frame.
Try moving closer or removing sunglasses/masks.
Slow processing

Use fewer megapixels (still capture is fine here).
In Interpreter.Options(), adjust threads: setNumThreads(2..4).
Consider GPU delegate if your model benefits (test battery/perf).
