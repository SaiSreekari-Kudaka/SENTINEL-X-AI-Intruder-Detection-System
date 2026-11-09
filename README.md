# 🛡️ SENTINEL-X — AI-Powered Intruder Detection System

> **An intelligent computer-vision security application for real-time face detection, intruder classification, live monitoring, alert snapshot capture, and demographic analysis.**

SENTINEL-X is a Python-based intruder detection system built with **Streamlit, OpenCV, and DeepFace**. The application processes live webcam video, detects faces in real time, and classifies detected individuals using a hybrid recognition approach based on **LBPH face recognition** and optional **DeepFace FaceNet512 embeddings**.

The system provides a complete monitoring interface rather than just a standalone machine-learning model. Users can register intruder and known-safe faces, train the recognition model, monitor a live camera feed, automatically save security snapshots, review detection evidence, and optionally analyze estimated age ranges and gender classifications.

---

## 🚀 Key Features

### 🎥 Real-Time Live Detection

- Live webcam monitoring using WebRTC
- Real-time face detection using OpenCV
- Face classification during video streaming
- Visual bounding boxes around detected faces
- Real-time security status indicators
- Face count and detection statistics

---

### 🧠 Hybrid Face Classification

SENTINEL-X supports a hybrid detection architecture:

- **Layer 1 — LBPH Face Recognition**
  - Fast local face classification
  - Trained using registered intruder face images

- **Layer 2 — DeepFace FaceNet512**
  - Optional deep-learning-based embedding comparison
  - Uses cosine similarity for face matching
  - Can be used when LBPH cannot confidently classify a face

The application gracefully falls back to **LBPH-only detection** if DeepFace is unavailable.

---

### 🚨 Intruder Alerts

When an intruder is detected, the system:

- Displays an intruder alert
- Highlights the detection visually
- Updates the live security status
- Automatically captures a snapshot
- Stores the detection as evidence
- Makes saved alerts available in the gallery

Snapshot intervals can be configured to control how frequently images are saved.

---

### 👤 Face Registration & Model Training

Users can build the recognition database by adding:

- 🔴 **Intruder faces**
- 🟢 **Known-safe faces**

Faces can be added through:

- Image upload
- Direct camera capture

The system automatically detects and crops faces before saving them to the appropriate dataset.

Users can then train or retrain the LBPH recognition model directly from the application interface.

---

### 📊 Security Dashboard

The dashboard provides an overview of the system, including:

- Number of registered intruder faces
- Number of known-safe faces
- Number of intruder alerts
- Number of safe snapshots
- Detection engine status
- LBPH model status
- DeepFace availability
- Age and gender model availability
- Recent intruder alert images

---

### 🖼️ Evidence Gallery

The Gallery provides access to captured images from:

- 🚨 Intruder alerts
- ✅ Safe detections
- 📂 Additional gallery captures

Users can:

- View captured images
- Download individual snapshots
- Delete individual images
- Clear stored images from a gallery category

---

### 👤 Age & Gender Analysis

SENTINEL-X includes optional demographic analysis using OpenCV DNN models.

When the required Caffe models are available, the application can estimate:

- Gender classification
- Approximate age range

Analysis can be performed on:

- Saved intruder alerts
- Safe snapshots
- User-uploaded images

> ⚠️ Age and gender predictions are model-generated estimates and should not be treated as definitive identification information.

---

### 🔐 User Authentication

The application includes a basic operator authentication system with:

- User login
- Account creation
- Password confirmation
- SHA-256 password hashing
- Session-based login state
- Logout functionality

User credentials are stored locally in `users.json`.

> ⚠️ This authentication mechanism is intended for demonstration and local project use. A production deployment should use stronger password hashing and a proper database-backed authentication system.

---

# 🏗️ System Architecture

```text
                        ┌──────────────────┐
                        │      OPERATOR    │
                        └────────┬─────────┘
                                 │
                                 ▼
                     ┌─────────────────────┐
                     │   STREAMLIT UI      │
                     │ Dashboard & Control │
                     └──────────┬──────────┘
                                │
                    ┌───────────┼───────────┐
                    │           │           │
                    ▼           ▼           ▼
              Live Camera   Face Training   Gallery
                    │           │           │
                    ▼           ▼           │
              Face Detection  Face Database │
                    │           │           │
                    ▼           ▼           │
              ┌──────────────────────┐     │
              │   HYBRID ENGINE      │     │
              │                      │     │
              │  Layer 1: LBPH       │     │
              │          ↓           │     │
              │ Layer 2: DeepFace    │     │
              │      FaceNet512      │     │
              └──────────┬───────────┘     │
                         │                 │
              ┌──────────┴──────────┐      │
              ▼                     ▼      │
         🚨 INTRUDER            ✅ SAFE    │
              │                     │      │
              ▼                     ▼      │
       Save Alert Snapshot   Save Snapshot │
              │                     │      │
              └──────────┬──────────┘      │
                         ▼                 │
                  Evidence Gallery ◄───────┘
```

---

# ⚙️ Detection Workflow

The application processes each detected face using the following workflow:

```text
Live Webcam Frame
        │
        ▼
OpenCV Face Detection
        │
        ▼
Face Region Extraction
        │
        ▼
┌─────────────────────────────┐
│ Layer 1: LBPH Recognition   │
└──────────────┬──────────────┘
               │
        ┌──────┴──────┐
        │             │
        ▼             ▼
   Intruder/Safe   Unknown
                      │
                      ▼
        ┌───────────────────────────┐
        │ Layer 2: DeepFace         │
        │ FaceNet512 Embeddings     │
        │ + Cosine Similarity       │
        └──────────────┬────────────┘
                       │
                       ▼
              Final Classification
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
     🚨 Intruder                 ✅ Safe
          │                         │
          ▼                         ▼
    Save Evidence              Save Snapshot
```

---

# 🧠 Detection Engine

## Layer 1 — LBPH Face Recognition

The primary recognition layer uses **Local Binary Patterns Histograms (LBPH)**.

LBPH is used for fast face recognition based on the registered intruder dataset.

### Workflow

```text
Registered Face Images
        ↓
Face Detection & Cropping
        ↓
Grayscale Conversion
        ↓
Face Normalization
        ↓
LBPH Model Training
        ↓
intruder_lbph.yml
```

During live detection, the trained model compares detected faces against the learned intruder data.

---

## Layer 2 — DeepFace FaceNet512

When enabled, SENTINEL-X can use **DeepFace with FaceNet512** as an additional recognition layer.

The system:

1. Generates a facial embedding.
2. Compares it with stored embeddings.
3. Calculates cosine similarity.
4. Finds the best match.
5. Classifies the face when the similarity threshold is met.

This provides a deeper embedding-based comparison mechanism in addition to LBPH.

---

# 🖥️ Application Modules

## 📊 Dashboard

Provides a high-level overview of the system.

### Displays

- Intruder face database size
- Safe face database size
- Number of intruder alerts
- Number of safe snapshots
- Detection engine status
- Model availability
- Recent intruder alerts

---

## 🎥 Live Detection

Provides real-time webcam monitoring.

### Features

- Webcam video streaming
- Face detection
- LBPH classification
- Optional DeepFace classification
- Configurable detection thresholds
- Configurable snapshot intervals
- Detection confidence display
- Live face count
- Security status indicators

### Detection Parameters

Users can configure:

- LBPH confidence threshold
- DeepFace similarity threshold
- Snapshot interval
- Confidence value display
- DeepFace processing

---

## 🧠 Train Model

Allows users to build and manage the recognition datasets.

### Intruder Database

Users can:

- Upload multiple face images
- Capture faces using a camera
- Preview saved images
- Train the LBPH model

### Safe Face Database

Known-safe faces can also be registered to improve classification and comparison.

---

## 🖼️ Gallery

Provides a visual archive of security-related images.

### Categories

| Category | Purpose |
|---|---|
| 🚨 Intruder Alerts | Evidence captured during intruder detection |
| ✅ Safe Snapshots | Images captured during safe detections |
| 📂 Gallery Captures | Additional stored images |

Users can view, download, delete, or clear stored images.

---

## 👤 Age & Gender Analysis

Optional deep-learning-based analysis can be performed on stored or uploaded images.

The application uses OpenCV DNN models to estimate:

- **Gender:** Male / Female
- **Age Range:** Predicted age bucket

The feature requires the corresponding Caffe model files.

---

# 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| **Python** | Core application development |
| **Streamlit** | Interactive web application and dashboard |
| **OpenCV** | Computer vision and face detection |
| **OpenCV Contrib** | LBPH face recognition |
| **DeepFace** | Deep-learning-based face embeddings |
| **FaceNet512** | Facial feature representation |
| **TensorFlow** | Deep learning backend |
| **scikit-learn** | Cosine similarity calculations |
| **streamlit-webrtc** | Real-time webcam streaming |
| **NumPy** | Numerical processing |
| **Pillow** | Image handling |
| **Caffe Models** | Age and gender estimation |

---

# 📂 Project Structure

```text
SENTINEL-X-AI-Intruder-Detection-System/
│
├── app.py
│   └── Main Streamlit application
│       • Authentication
│       • Dashboard
│       • Live Detection
│       • Model Training
│       • Gallery
│       • Age & Gender Analysis
│
├── requirements.txt
│   └── Python dependencies
│
├── SETUP.md
│   └── Installation and setup instructions
│
├── users.json
│   └── Local user credentials
│
├── age_deploy.prototxt.txt
├── age_net.caffemodel
│   └── Age estimation model resources
│
├── gender_deploy.prototxt.txt
├── gender_net.caffemodel
│   └── Gender classification model resources
│
└── Runtime-generated directories
    │
    ├── intruder_faces/
    │   └── Registered intruder face dataset
    │
    ├── normal_faces/
    │   └── Known-safe face dataset
    │
    ├── snap_intruder/
    │   └── Captured intruder alert evidence
    │
    ├── snap_safe/
    │   └── Captured safe detection snapshots
    │
    └── gallery/
        └── Additional stored images
```

---

# 🚀 Installation

## Prerequisites

Ensure that you have:

- Python **3.9+** recommended
- pip
- A working webcam
- Internet access for dependency installation

---

## 1. Clone the Repository

```bash
git clone https://github.com/SaiSreekari-Kudaka/SENTINEL-X-AI-Intruder-Detection-System.git
```

---

## 2. Navigate to the Project Directory

```bash
cd SENTINEL-X-AI-Intruder-Detection-System
```

---

## 3. Create a Virtual Environment

### Windows

```bash
python -m venv venv
venv\Scripts\activate
```

### macOS/Linux

```bash
python3 -m venv venv
source venv/bin/activate
```

---

## 4. Install Dependencies

```bash
pip install -r requirements.txt
```

The project includes dependencies for:

- Streamlit
- OpenCV
- OpenCV Contrib
- NumPy
- Pillow
- Streamlit WebRTC
- DeepFace
- TensorFlow
- scikit-learn

---

# ▶️ Running the Application

Start SENTINEL-X with:

```bash
streamlit run app.py
```

Streamlit will provide a local URL in the terminal, typically:

```text
http://localhost:8501
```

Open the URL in your browser to access the application.

---

# 🔐 Default Login

The project currently includes a default local account:

```text
Username: admin
Password: admin123
```

> ⚠️ Change or remove default credentials before deploying the application outside a local development environment.

---

# 📷 Setting Up Face Detection

## Step 1 — Register Intruder Faces

1. Open **Train Model**.
2. Select **Intruder Faces**.
3. Upload face images or capture images using the camera.
4. Ensure faces are clearly visible.

---

## Step 2 — Register Known-Safe Faces

Optionally add images of known-safe individuals.

1. Select **Safe/Known Faces**.
2. Upload or capture face images.
3. Save the detected faces.

---

## Step 3 — Train the Model

Click:

```text
⚡ TRAIN LBPH MODEL
```

The application trains the LBPH model and saves it locally.

---

## Step 4 — Start Live Monitoring

Open:

```text
🎥 Live Detection
```

Start the webcam stream.

The system will:

- Detect faces
- Classify them
- Display the classification
- Trigger an intruder alert when appropriate
- Save snapshots automatically

---

# 👤 Optional Age & Gender Models

Age and gender analysis requires OpenCV DNN Caffe models.

Place the required model files in the project root.

The application supports the following resources:

```text
age_net.caffemodel
age_deploy.prototxt

gender_net.caffemodel
gender_deploy.prototxt
```

If these models are unavailable, the core intruder detection functionality can still operate.

---

# ⚙️ Configuration

The Live Detection page allows runtime configuration of key parameters.

### LBPH Confidence Threshold

Controls how strictly LBPH classifications are interpreted.

### DeepFace Similarity Threshold

Controls the minimum cosine similarity required for an embedding match.

### Snapshot Interval

Controls how frequently snapshots are automatically saved.

### DeepFace Layer

Can be enabled or disabled when DeepFace is available.

---

# 📊 Example Use Cases

SENTINEL-X can be adapted for security-monitoring scenarios such as:

- 🏠 Home security monitoring
- 🏢 Office entry monitoring
- 🔬 Computer vision experimentation
- 🎓 Academic projects
- 🧪 Face recognition research prototypes
- 🚪 Restricted-area monitoring

> This project is intended as a software demonstration and educational computer-vision system, not as a certified physical security product.

---

# 🔐 Security Considerations

The project includes basic local authentication, but the current implementation should **not be considered production-grade authentication**.

### Current Implementation

- Passwords are hashed using SHA-256.
- User credentials are stored locally in JSON.
- Session state controls access to the application.

### Recommended Production Improvements

- Use a dedicated password hashing algorithm such as **Argon2** or **bcrypt**
- Store users in a secure database
- Add authentication rate limiting
- Implement role-based access control
- Use HTTPS
- Add audit logging
- Protect stored surveillance data
- Implement secure backup and retention policies

---

# ⚠️ Privacy & Responsible Use

Face recognition and demographic analysis involve sensitive technologies.

When using or extending SENTINEL-X:

- Obtain appropriate consent where required.
- Follow applicable privacy and data-protection laws.
- Protect captured images and biometric data.
- Avoid using demographic estimates as definitive identification.
- Do not use the system for unlawful surveillance or discriminatory decision-making.

---

# 🔮 Future Improvements

Potential improvements include:

### 🧠 AI & Detection

- [ ] Multi-class person identification
- [ ] Improved face recognition datasets
- [ ] Liveness detection
- [ ] Anti-spoofing protection
- [ ] Object and person detection
- [ ] Improved confidence calibration

### 🚨 Security Features

- [ ] Email alerts
- [ ] SMS notifications
- [ ] Push notifications
- [ ] Alarm integration
- [ ] Event audit logs
- [ ] Incident reports

### 💾 Data Management

- [ ] Database integration
- [ ] Searchable detection history
- [ ] Timestamp-based event logs
- [ ] Automated evidence retention policies

### 🌐 Deployment

- [ ] Docker support
- [ ] Cloud deployment
- [ ] Production authentication
- [ ] CI/CD pipeline
- [ ] Secure environment configuration

---

# 🎓 Learning Outcomes

This project demonstrates practical experience with:

- Python application development
- Computer vision with OpenCV
- Real-time webcam processing
- Face detection and recognition
- LBPH model training
- Deep learning face embeddings
- Cosine similarity matching
- Streamlit application development
- WebRTC-based video streaming
- Image processing
- Model integration
- Security dashboard development
- Authentication fundamentals

---

# 👩‍💻 Developer

**Sai Sreekari Kudaka**

B.Tech Student | Software Engineering & Cybersecurity Enthusiast

### Areas of Interest

- 💻 Software Engineering
- 🔐 Cybersecurity
- 🤖 Artificial Intelligence
- 👁️ Computer Vision
- 🌐 Full-Stack Development

---

# 🔗 Repository

[SENTINEL-X AI Intruder Detection System](https://github.com/SaiSreekari-Kudaka/SENTINEL-X-AI-Intruder-Detection-System?utm_source=chatgpt.com)

---

<div align="center">

## 🛡️ SENTINEL-X

### AI-Powered • Real-Time • Hybrid Face Recognition • Security Monitoring

**Built with Python, Streamlit, OpenCV, LBPH, DeepFace, FaceNet512, and TensorFlow**

⭐ If you find this project useful or interesting, consider giving the repository a star.

</div>
