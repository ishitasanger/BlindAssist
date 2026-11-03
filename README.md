# 👁️ BlindAssist — AI-Powered Smart Cane Assistance System

### Turning a Conventional White Cane into an AI-Assisted Mobility Companion

BlindAssist is a camera-based AI assistive technology prototype designed to improve environmental awareness for visually impaired individuals, particularly in the diverse and unpredictable street environments of India.

The project explores integrating a camera with a conventional white cane and connecting it to a laptop or compatible processing device. Computer vision analyses the camera feed, identifies supported objects, estimates their relative direction and proximity, and communicates contextual information through voice alerts in Hindi or English.

Unlike a basic object detection application, BlindAssist aims to answer three important questions:

- *WHAT* is around the user?
- *WHERE* is the object located relative to the user?
- *HOW NEAR* does the object appear to be?

The goal is to supplement the conventional white cane with an additional layer of AI-powered environmental awareness.

---

## 📌 Table of Contents

1. [The Problem: Indian Street Environments](#-the-problem-indian-street-environments)
2. [Our Solution](#-our-solution)
3. [Cane-Mounted Camera System](#-cane-mounted-camera-system)
4. [Clock-Based Direction Detection](#-clock-based-direction-detection)
5. [Core Features](#-core-features)
6. [How the System Works](#-how-the-system-works)
7. [Technical Architecture](#-technical-architecture)
8. [Distance Estimation](#-distance-estimation)
9. [Predictive Obstacle Monitoring](#-predictive-obstacle-monitoring)
10. [Voice Alert System](#-voice-alert-system)
11. [Technology Stack](#-technology-stack)
12. [Installation and Setup](#-installation-and-setup)
13. [Current Implementation Status](#-current-implementation-status)
14. [Limitations and Safety](#-limitations-and-safety)
15. [Future Scope](#-future-scope)

---

## 🇮🇳 The Problem: Indian Street Environments

Navigating Indian streets can be particularly challenging for visually impaired individuals because pedestrian environments are often unpredictable.

Unlike controlled walking environments, Indian streets may contain:

- Potholes and uneven road surfaces.
- Open drains and unexpected gaps.
- Vehicles parked on footpaths.
- Moving cars, motorcycles and auto-rickshaws.
- Street vendors and temporary stalls.
- Construction materials and roadside obstructions.
- Pedestrians moving in different directions.
- Objects protruding into walking pathways.

A conventional white cane is an essential mobility aid. However, it primarily provides information through physical contact and does not independently identify every object or explain its visual context.

For example, detecting an obstacle is useful, but knowing whether it is a person, a parked vehicle or another object can provide additional environmental information.

BlindAssist explores how computer vision can help bridge this information gap.

---

## 💡 Our Solution

BlindAssist is designed around a camera mounted securely on a conventional white cane.

The camera captures the user's surroundings and transmits the video stream to a connected laptop, where the AI processing takes place.

The system then:

1. Processes incoming camera frames.
2. Detects supported objects using YOLO.
3. Analyses the position of detected objects.
4. Estimates their relative proximity using depth-related techniques.
5. Tracks objects across successive frames.
6. Generates contextual alerts.
7. Communicates relevant information through Hindi or English voice assistance.

The system is designed to supplement, not replace, conventional mobility aids.

---

## 📷 Cane-Mounted Camera System

### Hardware Concept

The central hardware concept is to integrate a camera with an existing white cane rather than requiring users to purchase an entirely new electronic mobility device.

*Proposed setup:*

- A conventional white cane.
- A securely attached forward-facing camera.
- A laptop or compatible processing device.
- A wireless or USB camera connection.
- Audio output through connected speakers or headphones.

### Camera Placement and Orientation

Camera positioning is critical because the AI uses the captured image to estimate the relative direction of surrounding objects.

The intended mounting arrangement follows these principles:

1. The camera should be firmly secured to the cane.
2. The lens should face forward in the direction of travel.
3. The camera should maintain a consistent orientation during use.
4. Its field of view should include the relevant walking environment.
5. Mounting should avoid obstruction of the lens by the cane or the user's hand.
6. The camera's orientation should be calibrated before interpreting directional alerts.

A forward-facing camera provides the visual input needed to analyse objects in front of and around the user.

### Camera Input Options

BlindAssist is designed to support multiple camera configurations:

| Camera type | Purpose |
|---|---|
| Wireless/IP webcam | Preferred approach for the cane-mounted wireless camera |
| Android IP camera | Alternative camera input using an Android phone |
| USB webcam | Local development and testing using a laptop or external USB camera |
| Laptop built-in webcam | Initial prototype and development testing |

The laptop performs the computer vision processing. A remote camera transmits frames over a compatible network, while a locally connected USB camera can be used during development.

### Wireless Camera Workflow

For wireless camera operation:

1. Mount the camera securely on the white cane.
2. Connect the camera and laptop to a compatible Wi-Fi network or hotspot.
3. Obtain the actual camera stream URL from the camera application or device.
4. Configure the stream URL in BlindAssist.
5. Verify that frames are being received.
6. Start object detection and audio processing.

The camera stream URL must be configured for the actual device; IP addresses and stream paths should never be guessed.

---

## 🕒 Clock-Based Direction Detection

One of the important design objectives of BlindAssist is to communicate object positions using familiar clock directions.

Instead of restricting directional information to only Left, Centre and Right, the system can describe an object's relative position using a clock-face representation.

Imagine the user standing at the centre of a clock, facing 12 o'clock.

| Direction | Interpretation |
|---|---|
| 9 o'clock | Directly to the left |
| 10 o'clock | Front-left |
| 11 o'clock | Slightly front-left |
| 12 o'clock | Directly ahead |
| 1 o'clock | Slightly front-right |
| 2 o'clock | Front-right |
| 3 o'clock | Directly to the right |

Additional directions can be introduced if the camera's field of view and calibration support them.

### How Direction Is Calculated

The detector returns a bounding box for each detected object.

The system calculates the centre of that bounding box:

\[
x_c = \frac{x_{\min}+x_{\max}}{2}
\]

\[
y_c = \frac{y_{\min}+y_{\max}}{2}
\]

The horizontal centre is compared with the image width:

\[
x_{\text{normalised}} = \frac{x_c}{W}
\]

Where:

- \(x_c\) is the horizontal centre of the detected object.
- \(W\) is the image width.

The horizontal position is then mapped to a directional region.

For more detailed positioning, the system can use five configurable regions:

1. Far Left
2. Left-Centre
3. Centre
4. Right-Centre
5. Far Right

These regions can be mapped to clock-based descriptions such as 9, 11, 12, 1 and 3 o'clock.

*Important:* Clock directions represent camera-image directions interpreted relative to the user's forward-facing orientation. They are not precise world coordinates. Correct camera mounting and orientation calibration are necessary.

### Example Voice Alerts

- "Person detected at 12 o'clock."
- "Vehicle detected at 10 o'clock."
- "Obstacle detected at 1 o'clock."

Directional alerts communicate object position; they do not independently establish a safe route or tell the user to change walking direction.

---

## ✨ Core Features

### 1. AI-Based Object Detection

Uses the pretrained YOLOv8n model as the baseline detection system.

- Detects supported object classes.
- Produces bounding boxes and confidence scores.
- Processes camera frames continuously.
- Provides object labels for subsequent spatial analysis.

Navigation-specific fine-tuning is an intended enhancement to improve recognition of hazards that general-purpose object detection models may not recognise reliably.

### 2. Spatial Awareness

Combines object positioning with directional interpretation.

- Identifies relative horizontal object positions.
- Supports five configurable image regions.
- Explores clock-based spoken directions.
- Accounts for camera orientation and mounting configuration.

### 3. Depth Estimation

Explores two approaches to estimating the relative proximity of objects:

- Known-size estimation using camera calibration.
- Monocular depth estimation using models such as MiDaS or Depth Anything.

The objective is to provide meaningful proximity information rather than simply identify objects.

### 4. Predictive Obstacle Monitoring

Uses temporal information to analyse how detected objects change position across frames.

The proposed ByteTrack integration aims to:

- Maintain object identities across frames.
- Track moving objects.
- Observe changes in relative position.
- Identify potentially approaching obstacles.
- Reduce repeated alerts for the same object.

### 5. Multilingual Voice Assistance

Provides audio-based environmental information in:

- English
- Hindi

Alerts are generated based on detected objects, relative direction, proximity information where supported, and selected alert settings.

### 6. Dual-Camera Compatibility

Supports the project's two intended camera approaches:

- USB camera input.
- Android IP camera stream.

This provides flexibility for testing and eventual cane-mounted camera integration.

### 7. Alert Management

The alert manager uses a configurable cooldown mechanism to reduce repetitive notifications.

It is designed to prioritise relevant environmental information rather than continuously announcing every detection.

---

## ⚙️ How the System Works

BlindAssist follows a camera-to-AI-to-audio processing pipeline.

mermaid
flowchart TD
    A["Cane-Mounted Camera"] --> B["USB / Wireless IP / Android Stream"]
    B --> C["Camera Frame Processing"]
    C --> D["YOLOv8n Object Detection"]
    D --> E["Bounding Box and Object Labels"]
    E --> F["Spatial Position Analysis"]
    F --> G["Depth Estimation"]
    G --> H["ByteTrack / Temporal Analysis"]
    H --> I["Context-Aware Alert Manager"]
    I --> J["Hindi / English Speech"]
    J --> K["Audio Feedback"]


### Processing Stages

*Stage 1 — Camera Input*

Captures the surrounding environment using a cane-mounted camera or compatible development camera.

*Stage 2 — Frame Processing*

Receives video frames, handles image conversion and prepares frames for detection.

*Stage 3 — Object Detection*

Uses YOLOv8n to identify supported objects and their bounding boxes.

*Stage 4 — Spatial Analysis*

Calculates the object's relative image position and maps it to a directional zone or clock description.

*Stage 5 — Distance Estimation*

Investigates object proximity using calibrated known-size estimation or monocular depth estimation.

*Stage 6 — Temporal Analysis*

Uses object tracking and frame history to monitor changes in detected object positions.

*Stage 7 — Context-Aware Alerts*

Combines object labels, directional information and available proximity information to produce concise alerts.

*Stage 8 — Audio Feedback*

Communicates relevant alerts through the selected language.

---

## 📏 Distance Estimation

Distance estimation is an important development objective because identifying an object without understanding its approximate proximity may provide incomplete information.

BlindAssist explores two approaches.

### Approach A — Known-Size Estimation

Uses the pinhole camera model:

\[
D \approx \frac{S \times F}{P}
\]

Where:

- \(D\) = estimated distance.
- \(S\) = assumed real-world object size.
- \(F\) = calibrated focal length in pixels.
- \(P\) = observed object size in pixels.

A one-time calibration process can estimate focal length using an object of known dimensions at a measured distance.

The method depends on the accuracy of the object-size assumption and the camera calibration.

### Approach B — Monocular Depth Estimation

Explores depth models such as:

- MiDaS-small.
- Compatible Depth Anything variants.

The model generates a depth map from a single camera frame.

The depth information can be associated with detected object regions to estimate relative proximity.

*Important:* Monocular depth outputs are not automatically metric distances in metres. Calibration and controlled evaluation are necessary before presenting metre-based distance estimates.

### Example Output

"Object detected ahead, approximately 1.5 metres away."

This type of metric alert should only be enabled when the distance estimation method has been calibrated and validated under the relevant conditions.

Otherwise, the system should use qualitative descriptions such as "appears nearer" or "appears farther."

---

## 🔄 Predictive Obstacle Monitoring

A single camera frame provides only a snapshot of the environment.

To understand how an object is moving, BlindAssist explores tracking objects across multiple frames.

### ByteTrack Integration

ByteTrack is the planned tracking approach.

It can help associate detections across successive frames and maintain object identities.

The system can use this temporal information to:

- Monitor changes in object positions.
- Identify objects moving towards the camera's field of view.
- Reduce duplicate notifications.
- Generate more context-aware alerts.

Temporal smoothing can also help reduce false-positive alerts. An ordinary alert can require a matching detection in at least three of the last five processed frames, where feasible, without delaying potentially urgent alerts unnecessarily.

---

## 🔊 Voice Alert System

The voice alert system is designed to communicate useful information concisely.

### Example Alerts

| Situation | Example alert |
|---|---|
| Person ahead | "Person ahead, at 12 o'clock." |
| Vehicle on left | "Vehicle on your left, at 10 o'clock." |
| Object on right | "Obstacle detected at 1 o'clock." |
| Nearby object | "Object appears very close." |
| Distance validated | "Object detected approximately 1.5 metres away." |

### Language Support

- English voice alerts.
- Hindi voice alerts.

### Alert Modes

| Mode | Purpose |
|---|---|
| Normal | Standard obstacle-related notifications |
| Quiet | Reduced notification frequency |
| Detailed | More descriptive environmental feedback |

The alert manager uses cooldown and contextual processing to avoid overwhelming the user with repeated messages.

Distance should not be announced on every frame. Alerts should be generated when relevant information changes.

---

## 🏗️ Technical Architecture

| Component | Responsibility |
|---|---|
| Camera input | Captures the surrounding environment |
| OpenCV | Camera and frame processing |
| YOLOv8n | Baseline object detection |
| Spatial analysis | Image position and clock-direction mapping |
| Distance estimator | Known-size and monocular-depth approaches |
| ByteTrack | Planned object tracking |
| Alert Manager | Contextual alert generation |
| Speech Module | Multilingual audio output |
| Streamlit | Monitoring and configuration dashboard |

---

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| Python | Core application |
| OpenCV | Camera capture and image processing |
| Ultralytics YOLOv8n | Object detection |
| ByteTrack | Object tracking |
| MiDaS / Depth Anything | Experimental monocular depth estimation |
| NumPy | Numerical processing |
| Streamlit | Application dashboard |
| Streamlit-WebRTC | Browser-based camera input |
| pyttsx3 | Offline speech where supported |
| gTTS | Optional internet-dependent speech generation |
| Pygame | Optional audio playback |

---

## 📁 Project Structure

text
BlindAssist/
│
├── app.py
│
├── vision/
│   ├── detector.py
│   └── stream.py
│
├── alerts/
│   ├── alert_manager.py
│   ├── speech.py
│   └── audio/
│
├── config.py
├── requirements.txt
├── yolov8n.pt
└── README.md


---

## 🚀 Installation and Setup

### Prerequisites

- Python environment compatible with the project dependencies.
- Git.
- Laptop with a working camera.
- Optional USB or wireless/IP camera.
- A conventional white cane for the intended hardware demonstration.
- Wi-Fi or hotspot for wireless camera streaming.

### 1. Clone the Repository

bash
git clone https://github.com/Nikita-Gupta280/BlindAssist.git
cd BlindAssist


### 2. Create a Virtual Environment

bash
python -m venv venv


Windows:

bash
venv\Scripts\activate


Linux / macOS:

bash
source venv/bin/activate


### 3. Install Dependencies

bash
pip install -r requirements.txt


### 4. Run the Application

bash
streamlit run app.py


The application will generally be available at:

text
http://localhost:8501


Allow browser camera access when prompted.

---

## 📊 Current Implementation Status

BlindAssist distinguishes between demonstrated components and ongoing development objectives.

| Component | Status |
|---|---|
| Streamlit dashboard | Implemented |
| YOLOv8n-based object detection | Implemented |
| USB webcam prototype | Demonstrated |
| Detection visualisation | Implemented |
| English and Hindi alert configuration | Implemented |
| Clock-direction mapping | Development objective |
| Calibrated distance estimation | Requires implementation and validation |
| ByteTrack-based predictive monitoring | Planned integration |
| Android IP camera | Requires validation |
| Stable deployed WebRTC camera | Under deployment validation |
| Reliable browser audio alerts | Under development |
| Indian street-specific model fine-tuning | Future development |

---

## 🇮🇳 Why BlindAssist Is Designed for Indian Conditions

The project focuses on common challenges in Indian pedestrian environments rather than assuming that all walking environments are uniform.

The intended development priorities include:

- Recognising common road and pedestrian obstacles.
- Improving detection of potholes and open drains through suitable datasets and models.
- Handling crowded street environments.
- Identifying stationary and moving vehicles.
- Supporting multilingual audio feedback.
- Keeping hardware requirements affordable and adaptable.
- Allowing conventional white cane integration.

The baseline YOLO model does not reliably recognise every Indian street hazard. In particular, potholes, stairs, open drains, glass surfaces and unusual obstructions may require specialised training and evaluation.

Navigation-specific fine-tuning is therefore an important future development objective.

---

## ⚠️ Limitations and Safety

BlindAssist is an experimental assistive-technology prototype, not a certified navigation device.

- Object detection depends on lighting, camera quality and model capability.
- The baseline model may miss important obstacles.
- Clock directions depend on correct camera mounting and calibration.
- Distance estimates may be uncertain.
- Cloud processing may introduce latency.
- Camera streaming may be affected by network connectivity.
- Voice output may depend on the operating system and available audio devices.

The system must supplement, not replace, a conventional white cane, guide dog or established mobility techniques.

A missed detection must never be interpreted as evidence that a walking path is safe. Real-world trials require controlled testing and evaluation with visually impaired users and mobility specialists.

---

## 🔮 Future Scope

- Compact, secure cane-mounted camera hardware.
- Wireless camera integration and reliable Android IP streaming.
- Improved detection of Indian road hazards.
- Calibrated depth estimation and proximity alerts.
- Reliable object tracking and predictive monitoring.
- Improved Hindi and additional Indian-language support.
- Offline processing to reduce internet dependency.
- Reduced latency and more reliable audio feedback.
- Structured field testing and accessibility evaluation.

---

## 🔗 Project Links

- *Demo Video:* [Watch Demonstration](https://drive.google.com/file/d/1CDTJ0sJ-_GfmFUqyIDP8dCcxYcEMl__m/view?usp=drivesdk)

---

## ❤️ Our Mission

A conventional white cane helps detect obstacles through physical contact.

BlindAssist aims to add another layer of awareness by helping users understand what an obstacle is, where it appears and how near it may be.

*We are exploring how accessible AI, affordable camera hardware and multilingual voice assistance can contribute to safer and more informed mobility in Indian environments.*

### BlindAssist — More Than Object Detection. A Step Towards Intelligent Mobility Assistance.

