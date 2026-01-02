# SENTINEL-DF: Technology Stack & Architecture

## Core Technology Stack

### 1. AI/ML Framework
**Primary:**
- **TensorFlow 2.x / PyTorch 1.13+** - Deep learning framework
- **TensorFlow Lite** - Edge deployment & mobile optimization
- **ONNX Runtime** - Cross-platform inference

**Pre-trained Models:**
- **EfficientNet-B4** - Facial artifact detection (lightweight, fast)
- **Xception** - Video frame analysis (proven on FaceForensics++)
- **Wav2Vec 2.0** - Audio deepfake detection
- **MesoNet** - Mesoscopic properties analysis

**Why this works:**
- Pre-trained models = No training from scratch
- TFLite = Mobile deployment ready
- Proven models from research papers

---

### 2. Video & Audio Processing
**Libraries:**
- **OpenCV 4.x** - Video frame extraction, face detection
- **MediaPipe** - Face mesh detection, landmark tracking
- **FFmpeg** - Video/audio codec handling
- **Librosa** - Audio feature extraction
- **PyDub** - Audio manipulation

**Processing Pipeline:**
- Extract frames at 30fps
- Detect faces using MTCNN/MediaPipe
- Extract audio stream separately
- Parallel processing for speed

---

### 3. Mobile Application (Edge Deployment)
**Framework Options:**

**Option A - Flutter (Recommended):**
- **Flutter 3.x** - Cross-platform (Android + iOS)
- **TFLite Flutter Plugin** - ML model integration
- **Camera Plugin** - Real-time video capture
- **File Picker** - Upload existing videos

**Option B - React Native:**
- **React Native** - JavaScript-based
- **TensorFlow.js** - On-device inference
- **React Native Camera**

**Why Flutter:**
- Single codebase for Android + iOS
- Better performance than React Native
- Native TFLite support
- Easier to demo

---

### 4. Backend (Optional - For Scoring/Logging)
**Lightweight Backend:**
- **FastAPI (Python)** - Minimal API if needed
- **SQLite** - Local database on device
- **AES-256 Encryption** - Secure storage

**Note:** Keep it mostly edge-based (offline), backend optional

---

### 5. Security & Authentication Layer
**Components:**
- **RSA-2048** - Digital signatures for reports
- **SHA-256** - File integrity verification
- **HMAC** - Tamper-proof logging
- **Secure Enclave** (iOS) / Keystore (Android) - Credential storage

**Chain of Custody:**
- Timestamp all scans
- Log device info, GPS (if permitted)
- Cryptographic proof of authenticity

---

### 6. Visualization & UI
**Dashboard Components:**
- **Matplotlib/Seaborn** - Generate analysis graphs
- **Plotly** - Interactive visualizations
- **Custom Flutter Widgets** - Clean operative interface

**Cognitive Assistance:**
- Heat maps showing facial artifacts
- Audio waveform anomaly highlights
- Confidence breakdown (face: 85%, audio: 92%)
- Frame-by-frame analysis view

---

### 7. Development & Testing Tools
**Development:**
- **Jupyter Notebook** - Model testing
- **VS Code / Android Studio** - IDE
- **Git/GitHub** - Version control

**Testing:**
- **FaceForensics++ Dataset** - 1000+ deepfake videos
- **Celeb-DF Dataset** - Celebrity deepfakes
- **DFDC Dataset** - Deepfake Detection Challenge data
- **Custom test videos** - Various compression levels

---

## System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────┐
│              SENTINEL-DF SYSTEM                     │
├─────────────────────────────────────────────────────┤
│                                                     │
│  ┌─────────────┐         ┌──────────────┐         │
│  │   INPUT     │────────▶│  DETECTION   │         │
│  │   LAYER     │         │    AGENT     │         │
│  └─────────────┘         └──────────────┘         │
│       │                         │                   │
│       │                         ▼                   │
│       │                  ┌──────────────┐          │
│       │                  │   ANALYSIS   │          │
│       │                  │    ENGINE    │          │
│       │                  └──────────────┘          │
│       │                         │                   │
│       │                         ▼                   │
│       │                  ┌──────────────┐          │
│       │                  │   RESPONSE   │          │
│       │                  │    MODULE    │          │
│       │                  └──────────────┘          │
│       │                         │                   │
│       └─────────────────────────┼──────────────────┤
│                                 ▼                   │
│                          ┌──────────────┐          │
│                          │  OPERATIVE   │          │
│                          │  INTERFACE   │          │
│                          └──────────────┘          │
└─────────────────────────────────────────────────────┘
```

### Detailed Component Flow

```
┌──────────────────────────────────────────────────────┐
│ 1. INPUT LAYER                                       │
├──────────────────────────────────────────────────────┤
│                                                      │
│  • Live Camera Capture                              │
│  • File Upload (MP4, AVI, MOV)                      │
│  • Audio File (MP3, WAV)                            │
│  • Metadata Extraction                              │
│                                                      │
│  Output: Raw Video/Audio Streams                    │
└──────────────────────────────────────────────────────┘
              ▼
┌──────────────────────────────────────────────────────┐
│ 2. PREPROCESSING PIPELINE                            │
├──────────────────────────────────────────────────────┤
│                                                      │
│  Video Branch:              Audio Branch:           │
│  ┌────────────────┐        ┌───────────────┐       │
│  │ Frame Extractor│        │ Audio Isolator│       │
│  │ (30 fps)       │        │ (16kHz resample)│     │
│  └────────────────┘        └───────────────┘       │
│         ▼                          ▼                │
│  ┌────────────────┐        ┌───────────────┐       │
│  │ Face Detection │        │ Feature Extract│       │
│  │ (MediaPipe)    │        │ (MFCC, Spectral)│     │
│  └────────────────┘        └───────────────┘       │
│         ▼                          ▼                │
│  ┌────────────────┐        ┌───────────────┐       │
│  │ Face Alignment │        │ Noise Removal │       │
│  │ & Cropping     │        │               │       │
│  └────────────────┘        └───────────────┘       │
│                                                      │
│  Output: Processed Frames + Audio Features          │
└──────────────────────────────────────────────────────┘
              ▼
┌──────────────────────────────────────────────────────┐
│ 3. DETECTION AGENT (Autonomous AI)                   │
├──────────────────────────────────────────────────────┤
│                                                      │
│  Multi-Modal Detection:                             │
│                                                      │
│  ┌─────────────────────────────────────────┐        │
│  │ VIDEO ANALYSIS AGENT                    │        │
│  ├─────────────────────────────────────────┤        │
│  │ • EfficientNet-B4 (Facial Artifacts)   │        │
│  │ • Xception (Temporal Inconsistencies)   │        │
│  │ • Blink Rate Analysis                   │        │
│  │ • Lip-Sync Detection                    │        │
│  │ • Facial Landmark Tracking              │        │
│  └─────────────────────────────────────────┘        │
│                  ▼                                   │
│         Confidence: 0.87 (87%)                       │
│                                                      │
│  ┌─────────────────────────────────────────┐        │
│  │ AUDIO ANALYSIS AGENT                    │        │
│  ├─────────────────────────────────────────┤        │
│  │ • Wav2Vec 2.0 (Voice Cloning Detection)│        │
│  │ • Spectrogram Analysis                  │        │
│  │ • Prosody Analysis                      │        │
│  │ • Background Noise Consistency          │        │
│  └─────────────────────────────────────────┘        │
│                  ▼                                   │
│         Confidence: 0.92 (92%)                       │
│                                                      │
│  ┌─────────────────────────────────────────┐        │
│  │ METADATA VERIFICATION AGENT             │        │
│  ├─────────────────────────────────────────┤        │
│  │ • EXIF Data Analysis                    │        │
│  │ • Edit History Detection                │        │
│  │ • Compression Artifact Patterns         │        │
│  │ • Timestamp Verification                │        │
│  └─────────────────────────────────────────┘        │
│                  ▼                                   │
│         Anomaly Score: 0.78                          │
│                                                      │
│  Output: Multi-modal Detection Results              │
└──────────────────────────────────────────────────────┘
              ▼
┌──────────────────────────────────────────────────────┐
│ 4. ANALYSIS ENGINE (Fusion & Decision)               │
├──────────────────────────────────────────────────────┤
│                                                      │
│  ┌──────────────────────────────────────┐           │
│  │ WEIGHTED FUSION ALGORITHM            │           │
│  ├──────────────────────────────────────┤           │
│  │                                      │           │
│  │ Video Score:    0.87 × 0.45 = 0.39  │           │
│  │ Audio Score:    0.92 × 0.35 = 0.32  │           │
│  │ Metadata Score: 0.78 × 0.20 = 0.16  │           │
│  │                        ─────────────  │           │
│  │ Final Score:              0.87 (87%) │           │
│  │                                      │           │
│  │ Threshold: 0.75 (Configurable)      │           │
│  │ Decision: DEEPFAKE DETECTED ⚠️       │           │
│  └──────────────────────────────────────┘           │
│                                                      │
│  ┌──────────────────────────────────────┐           │
│  │ THREAT CLASSIFICATION                │           │
│  ├──────────────────────────────────────┤           │
│  │ Confidence: 87% → HIGH CONFIDENCE    │           │
│  │ Risk Level: CRITICAL                 │           │
│  │ Recommended Action: FLAG & REPORT    │           │
│  └──────────────────────────────────────┘           │
│                                                      │
│  Output: Classification + Confidence + Reasoning     │
└──────────────────────────────────────────────────────┘
              ▼
┌──────────────────────────────────────────────────────┐
│ 5. RESPONSE MODULE (Autonomous Actions)              │
├──────────────────────────────────────────────────────┤
│                                                      │
│  Automated Responses:                               │
│                                                      │
│  ✅ Generate Detailed Report                        │
│     • Frame-by-frame analysis                       │
│     • Artifact visualization (heat maps)            │
│     • Audio anomaly graphs                          │
│     • Confidence breakdown                          │
│                                                      │
│  ✅ Secure Logging                                  │
│     • Encrypted storage (AES-256)                   │
│     • Timestamp + GPS (if available)                │
│     • Device signature                              │
│     • Tamper-proof chain of custody                 │
│                                                      │
│  ✅ Alert Generation                                │
│     • Visual alert (red/green)                      │
│     • Audio notification (optional)                 │
│     • Share report option                           │
│                                                      │
│  ✅ Cognitive Assistance                            │
│     • Highlight suspicious regions                  │
│     • Explain WHY it's fake                         │
│     • Suggest verification steps                    │
│                                                      │
│  Output: Actionable Intelligence for Operative       │
└──────────────────────────────────────────────────────┘
              ▼
┌──────────────────────────────────────────────────────┐
│ 6. OPERATIVE INTERFACE (User Layer)                 │
├──────────────────────────────────────────────────────┤
│                                                      │
│  ┌─────────────────────────────────────┐            │
│  │   📱 MOBILE APP INTERFACE           │            │
│  ├─────────────────────────────────────┤            │
│  │                                     │            │
│  │  [🎥 SCAN VIDEO] [📤 UPLOAD FILE]  │            │
│  │                                     │            │
│  │  ┌───────────────────────────────┐ │            │
│  │  │ 🔴 DEEPFAKE DETECTED          │ │            │
│  │  │                               │ │            │
│  │  │ Confidence: 87%               │ │            │
│  │  │ Risk Level: HIGH              │ │            │
│  │  │                               │ │            │
│  │  │ [📊 VIEW DETAILS]             │ │            │
│  │  │ [💾 SAVE REPORT]              │ │            │
│  │  │ [📤 SHARE SECURELY]           │ │            │
│  │  └───────────────────────────────┘ │            │
│  │                                     │            │
│  │  Detailed Analysis:                 │            │
│  │  ├─ Video Score: 87% ⚠️            │            │
│  │  ├─ Audio Score: 92% ⚠️            │            │
│  │  └─ Metadata: Anomaly Detected      │            │
│  │                                     │            │
│  │  [📋 HISTORY] [⚙️ SETTINGS]        │            │
│  │                                     │            │
│  └─────────────────────────────────────┘            │
│                                                      │
└──────────────────────────────────────────────────────┘
```

---

## Edge Deployment Architecture

```
┌────────────────────────────────────────────────┐
│         MOBILE DEVICE (Edge Computing)         │
├────────────────────────────────────────────────┤
│                                                │
│  ┌──────────────────────────────────────────┐ │
│  │    APPLICATION LAYER (Flutter)           │ │
│  │  • Camera Access                         │ │
│  │  • File Management                       │ │
│  │  • UI Rendering                          │ │
│  │  • Secure Storage                        │ │
│  └──────────────────────────────────────────┘ │
│                    ▼                           │
│  ┌──────────────────────────────────────────┐ │
│  │    INFERENCE ENGINE                      │ │
│  │  • TensorFlow Lite Models                │ │
│  │  • ONNX Runtime                          │ │
│  │  • Model Optimization                    │ │
│  │    (Quantization, Pruning)               │ │
│  └──────────────────────────────────────────┘ │
│                    ▼                           │
│  ┌──────────────────────────────────────────┐ │
│  │    DEVICE HARDWARE                       │ │
│  │  • CPU/GPU Processing                    │ │
│  │  • Neural Engine (iOS)                   │ │
│  │  • Android NNAPI                         │ │
│  │  • Memory Management                     │ │
│  └──────────────────────────────────────────┘ │
│                                                │
│  ⚡ Processing Time: < 3 seconds per video    │
│  📵 No Internet Required                       │
│  🔋 Battery Optimized                         │
│                                                │
└────────────────────────────────────────────────┘
```

---

## Security Architecture

```
┌────────────────────────────────────────────────────┐
│              SECURITY LAYERS                       │
├────────────────────────────────────────────────────┤
│                                                    │
│  Layer 1: Input Validation                        │
│  ├─ File type verification                        │
│  ├─ Size limits                                   │
│  └─ Malware scanning                              │
│                                                    │
│  Layer 2: Processing Security                     │
│  ├─ Sandboxed execution                           │
│  ├─ Memory encryption                             │
│  └─ Secure temp storage                           │
│                                                    │
│  Layer 3: Data Security                           │
│  ├─ AES-256 encryption                            │
│  ├─ Secure enclave storage                        │
│  └─ Auto-wipe on tamper                           │
│                                                    │
│  Layer 4: Authentication                          │
│  ├─ Biometric auth (fingerprint/face)            │
│  ├─ PIN/Password backup                           │
│  └─ Session timeout                               │
│                                                    │
│  Layer 5: Audit Trail                             │
│  ├─ Immutable logging                             │
│  ├─ Digital signatures (RSA-2048)                 │
│  ├─ Timestamp verification                        │
│  └─ Chain of custody tracking                     │
│                                                    │
└────────────────────────────────────────────────────┘
```

---

## Performance Metrics (Target)

| Metric | Target | Actual (Estimated) |
|--------|--------|-------------------|
| **Detection Time** | < 3 seconds | 2.5 seconds |
| **Accuracy** | > 90% | 92-95% |
| **False Positive Rate** | < 5% | 3-4% |
| **App Size** | < 150 MB | ~120 MB |
| **RAM Usage** | < 500 MB | ~400 MB |
| **Battery Impact** | < 10% per hour | ~8% per hour |
| **Offline Operation** | 100% | ✅ |

---

## Deployment Platforms

### Supported Devices:
✅ **Android:** 8.0+ (API Level 26+)  
✅ **iOS:** 13.0+  
✅ **Tablets:** Android/iPad  
✅ **Body Cameras:** Custom Android builds  
✅ **Tactical Devices:** Rugged Android devices  

### Hardware Requirements:
- **Min RAM:** 2GB (4GB recommended)
- **Storage:** 500MB free space
- **Processor:** Quad-core 1.5GHz+
- **Camera:** 1080p+ (for live capture)

---

## Future Enhancements

### Phase 2 (Post-Hackathon):
- **Blockchain Integration:** Immutable verification records
- **Federated Learning:** Privacy-preserving model updates
- **Post-Quantum Cryptography:** Future-proof signatures
- **Multi-Language Support:** Hindi, Regional languages
- **Cloud Sync:** Optional secure cloud backup

### Phase 3 (Production):
- **Enterprise Dashboard:** Centralized monitoring
- **API Access:** Integration with existing systems
- **Custom Model Training:** Organization-specific models
- **Real-time Streaming:** Live video analysis
- **Multi-device Deployment:** Drones, CCTV systems

---

## Why This Tech Stack Wins

✅ **Modern & Proven:** Industry-standard tools  
✅ **Scalable:** Edge → Cloud architecture  
✅ **Secure:** Multiple security layers  
✅ **Practical:** Uses existing models, not experimental  
✅ **Feasible:** Can be built in 2-3 weeks  
✅ **Impressive:** Shows technical depth  
✅ **Future-Ready:** Extensible architecture  

This tech stack demonstrates:
- Deep technical knowledge
- Practical implementation skills
- Security-first mindset
- Real-world viability
- Innovation in deployment (edge-based)