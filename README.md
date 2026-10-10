# Assistive Vision AI 👁️🔊
### Real-Time Object Detection, Distance Estimation & Audio Guidance for Visually Impaired Users

**Assistive Vision AI** is an intelligent assistive vision software system designed to empower visually impaired individuals with independent spatial navigation. By processing a live camera stream in real time, the system identifies objects, calculates their distance in meters, evaluates contextual states (such as whether a door is open or closed), and delivers natural audio feedback directly to Bluetooth earbuds.

---

## 🎯 Key Capabilities

- **Real-Time Object Detection**: High-speed detection of obstacles and landmarks including doors, tables, chairs, people, and household objects powered by Ultralytics YOLOv8 and computer vision door analyzers.
- **Calibrated Distance Estimation**: Pinhole projection model calibrated with real-world object metrics to compute accurate distance in meters.
- **Contextual Spatial Understanding**:
  - **Directional Zones**: Categorizes objects into `Left Side`, `Ahead (Direct Path)`, and `Right Side`.
  - **Clock-Face Orientation**: Provides clock-position cues (e.g., *10 o'clock*, *12 o'clock*, *2 o'clock*).
  - **Door State Recognition**: Visual analysis evaluating whether a doorway is **Open** or **Closed**.
- **Natural Language Audio Guidance**:
  - Speaks clear, concise sentences matching human spatial perception:
    - *"Door is 2.5 meters ahead. Door is closed."*
    - *"Door is open."*
    - *"Table is on the left side, 2.0 meters."*
    - *"Caution: Chair directly ahead, 1.2 meters."* (Urgent safety collision alert)
- **Zero-Latency Bluetooth Earbud Integration**: Direct Windows SAPI5 audio streaming to connected Bluetooth headsets with non-blocking threaded priority queuing.
- **Intelligent Cadence Debouncing**: Prevents auditory clutter by suppressing repetitive announcements unless the object moves significantly or state changes.
- **Hands-Free Voice Interaction**: Query the system with voice commands like *"What's around me?"* or *"Is the door open?"*.
- **Integrated Synthetic Environment Demo**: Includes an automated room generator to test and showcase door, table, and chair detection immediately, even without a physical webcam.

---

## 🏛️ System Architecture

```text
  +------------------+
  | Camera / Video   |
  +--------+---------+
           | (Live Frames)
           v
  +------------------+      +--------------------+
  | Unified Detector | ---> |  Door Analyzer     | (Open / Closed State)
  | (YOLOv8 + CV)    |      +--------------------+
  +--------+---------+                 |
           |                           v
           |                +--------------------+
           +--------------> | Distance Estimator | (Meters via Pinhole Model)
                            +--------------------+
                                       |
                                       v
                            +--------------------+
                            |   Spatial Engine   | (Left / Ahead / Right / Clock)
                            +--------------------+
                                       |
                                       v
                            +--------------------+
                            | Audio Feedback     | (Priority Queue, Debounce,
                            | Engine             |  Natural Language Synthesizer)
                            +--------------------+
                                       |
                                       v
                            +--------------------+
                            | Bluetooth Earbuds  |
                            | (System Audio Out) |
                            +--------------------+
```

---

## 🚀 Quick Start Guide

### 1. Environment Setup

Ensure Python 3.10+ is installed. Install all required dependencies:

```bash
pip install -r requirements.txt
```

### 2. Run the Interactive Demo (No Webcam Needed)

To experience the full detection and audio guidance pipeline immediately:

```bash
python demo/run_demo.py
```
This script automatically generates a simulated indoor scene featuring a door (approaching from distance, switching from closed to open), a dining table on the left, and a chair on the right.

To run the demo in terminal/headless mode:
```bash
python demo/run_demo.py --no-gui
```

### 3. Run Live with Webcam & Bluetooth Earbuds

1. Turn on your Bluetooth earbuds and ensure they are paired in **Windows Settings > Bluetooth & devices**.
2. Launch the live assistive vision assistant:

```bash
python main.py
```

To run with microphone voice control active:
```bash
python main.py --voice-control
```

---

## ⌨️ Controls & Shortcuts

| Hotkey | Action | Description |
| :--- | :--- | :--- |
| **`[SPACE]`** | **Pause / Resume** | Temporarily silence all guidance announcements. |
| **`[M]`** | **Mute / Unmute** | Toggle audio output without stopping detection. |
| **`[S]`** | **Scene Snapshot** | Immediately narrates full surroundings (*"What's around me?"*). |
| **`[V]`** | **Push-to-Talk** | Speak a voice command into the microphone. |
| **`[+]` / `[-]`** | **Focal Length** | Dynamically calibrate distance estimation for your camera. |
| **`[Q]` / `[ESC]`**| **Exit** | Safely release camera and audio hardware resources. |

---

## 🗣️ Supported Voice Commands

When `--voice-control` is enabled (or after pressing `[V]`):
- *"What's around me?"* / *"Describe surroundings"* &rarr; Narrates all visible obstacles.
- *"What is ahead?"* &rarr; Narrates obstacles directly in the walking path.
- *"Is the door open?"* &rarr; Informs user of door distance and state.
- *"Mute"* / *"Pause"* &rarr; Silences guidance.
- *"Resume"* &rarr; Resumes audio guidance.

---

## 📐 Distance Calibration Formula

The distance calculation employs the geometric pinhole camera model:

$$\text{Distance (m)} = \frac{\text{Real Height (m)} \times \text{Focal Length (px)}}{\text{Bounding Box Height (px)}}$$

Where:
- $\text{Real Height}$ is calibrated from typical architectural & object dimensions (e.g. Standard Door: $2.05\text{m}$, Table: $0.75\text{m}$, Chair: $0.85\text{m}$).
- $\text{Focal Length}$ defaults to $950\text{px}$ for standard $70^\circ$ FOV webcams at $1280 \times 720$, adjustable via `config.py` or the `[+]`/`[-]` hotkeys.

---

## 📁 Project Structure

```text
assistive-vision-ai/
├── config.py                  # Calibrated focal lengths, object heights, and audio configs
├── requirements.txt           # Dependency specifications
├── main.py                    # Primary live application entrypoint
├── README.md                  # System manual and architecture reference
├── modules/
│   ├── __init__.py
│   ├── detector.py            # Unified detector (YOLOv8 + custom Door detector)
│   ├── distance_estimator.py  # Pinhole projection & temporal smoothing
│   ├── door_analyzer.py       # Door structural detection & open/closed state classifier
│   ├── spatial_engine.py      # Left/Ahead/Right zones & clock-face orientation
│   ├── audio_feedback.py      # Non-blocking TTS engine, priority queue & Bluetooth output
│   ├── voice_controller.py    # Voice command recognition
│   └── visualizer.py          # HUD display with sectors, bounding boxes, & transcripts
└── demo/
    ├── generate_synthetic_feed.py # Generates simulated indoor testing footage
    ├── run_demo.py            # 1-click standalone demonstration script
    └── indoor_scene.mp4       # Generated indoor scene
```
