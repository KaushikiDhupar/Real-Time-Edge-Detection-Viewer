# Real-Time Edge Detection Viewer

A minimal scaffold for an Android app that captures camera frames, processes them with OpenCV (C++) via JNI, and renders the result using OpenGL ES. Also contains a tiny TypeScript web viewer that displays a sample processed frame.

---

## ✅ Features implemented (Android + Web)
**Android**
- Camera capture integration (placeholder uses `TextureView` / `SurfaceTexture`)
- JNI bridge skeleton for sending frames to native code
- Native C++ placeholders for OpenCV-based processing (Canny / Grayscale)
- OpenGL ES 2.0 renderer skeleton that expects processed frames as textures
- Basic modular structure (`/app`, `/jni`, `/gl`)

**Web (TypeScript)**
- Minimal TypeScript + HTML viewer
- Loads a sample processed image (base64 or static) and overlays FPS / resolution stats
- Ready for a simple `tsc` build

---

## 📷 Screenshots / GIF
*Add screenshots or a GIF here showing:*
- Raw camera feed
- Edge-detected output
- FPS overlay and resolution display

Example Markdown for adding screenshots:
```markdown
![App - Raw feed](screenshots/raw.png)
![App - Edge detection](screenshots/edge.png)
![Web viewer](screenshots/web_viewer.png)
```

---

## ⚙️ Setup instructions (NDK, OpenCV dependencies)

### Prerequisites
- Android Studio (Flamingo or later recommended)
- Android SDK (latest platform you target)
- Android NDK (side-by-side) + CMake
- OpenCV Android SDK (download from https://opencv.org/releases/)
- Node.js + npm (for the web viewer, optional)

### Android / NDK / OpenCV (high-level)
1. Install Android Studio and the Android NDK:
   - In Android Studio: **SDK Manager → SDK Tools → NDK (Side by side)** and **CMake**.

2. Download OpenCV Android SDK:
   - Unzip `OpenCV-android-sdk` and copy required `sdk/native/jni/include` and `sdk/native/libs/<arch>` into your project or reference them from CMake.

3. Example `CMakeLists.txt` snippet:
```cmake
cmake_minimum_required(VERSION 3.10)
project(edge_native)

# Adjust OPENCV_ANDROID_SDK to your local path or pass via -D
set(OpenCV_DIR ${CMAKE_SOURCE_DIR}/../../OpenCV-android-sdk/sdk/native/jni)
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

add_library(native-lib SHARED native-lib.cpp)
target_link_libraries(native-lib ${OpenCV_LIBS} android log)
```

4. Example Gradle (module `app/build.gradle`) additions:
```groovy
externalNativeBuild {
    cmake {
        path "src/main/cpp/CMakeLists.txt"
        version "3.22.1"
    }
}
ndkVersion "25.1.8937393"
```

5. Place prebuilt `.so` libs (if using) in `app/src/main/jniLibs/<abi>/`.

### Build & Run (Android)
- Open project in Android Studio
- Sync Gradle
- Build / Run on an Android device (camera permission required)
- Ensure min API supports Camera2 if you use it (API 21+ recommended)

---

## Web viewer (TypeScript) setup
1. Navigate to `/web`
2. Initialize (if you want a minimal build):
```bash
npm init -y
npm install --save-dev typescript
npx tsc --init
# edit tsconfig.json if needed (target ES2017, module commonjs or es6)
npx tsc
# open web/index.html in browser (file://) or serve with a simple static server
npx http-server -c-1 .  # if you have http-server installed
```

3. `index.ts` should populate a DOM `<img>` or `<canvas>` with a static processed frame and update FPS/resolution overlays.

---

## 🧠 Quick explanation of architecture

### High-level flow (Android → JNI → C++ OpenCV → OpenGL)
1. **Camera (Java/Kotlin)**  
   - Captures frames into a `SurfaceTexture` or `ImageReader`.
   - Converts frame into a byte buffer or `Bitmap`/`ByteArray`.

2. **JNI Bridge (Java ↔ C++)**  
   - Java calls a native method (e.g., `nativeProcessFrame(byte[] buffer, int width, int height)`).
   - Passes raw pixels (NV21 / RGBA / YUV) to C++.

3. **OpenCV (C++)**  
   - Wrap incoming buffer into `cv::Mat`.
   - Convert color space if necessary (YUV → BGR → GRAY).
   - Run algorithm (Canny / Gaussian / threshold).
   - Either return a new buffer to Java, or upload processed data directly to an OpenGL texture (preferred for performance).

4. **OpenGL ES Renderer**  
   - Receives texture (GL texture ID) or uploaded pixel data.
   - Uses a full-screen quad and fragment shader to display the texture.
   - Optionally apply additional shader effects (grayscale, invert) for bonus.

### Web Viewer
- Loads an exported sample frame (base64 or static PNG).
- Displays it in the DOM and overlays a small HUD showing FPS and resolution (sample values).
- Optional: connect to Android app via WebSocket / HTTP to stream processed frames (mock endpoint acceptable for the assessment).

---

## 🔁 Bonus ideas (optional)
- Toggle between raw and processed output with a UI button.
- Implement FPS measurement and log processing time (native side).
- Add simple GLSL shader effects (grayscale, color invert).
- Create a mock WebSocket endpoint on Android (or a small Node.js server) that the web viewer connects to and displays frames in near-real-time.

---

## 📂 Repo structure (example)
```
/app        # Android Kotlin/Java sources, manifests, gradle files
/jni        # C++ OpenCV processing (native-lib.cpp, CMakeLists.txt)
/gl         # OpenGL ES rendering classes / shaders
/web        # TypeScript web viewer (index.html, index.ts, assets/)
/screenshots
/README.md
```

---

## 📝 Notes
- This README is written for a scaffolded project. Replace placeholders with real implementations:
  - Camera pipeline in `app/src/...`
  - OpenCV code in `/jni` that actually converts and processes frames
  - OpenGL renderer implementation in `/gl`
  - TypeScript build configuration for `/web`

---

## Contact / Submission
- Push the full project to GitHub or GitLab
- Ensure commit history is meaningful (no one giant commit)
- Include working screenshots and setup notes in this README

