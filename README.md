
# Vision-Based Vehicle Detection and Distance Estimation

A computer vision project that detects vehicles in aerial video footage and estimates their approximate distance from the camera using YOLO object detection, OpenCV, and monocular camera calibration.

The system processes recorded video, identifies different vehicle categories, draws bounding boxes around detected vehicles, estimates distances for calibrated vehicle classes, and exports detection results into a CSV file.

## Project Overview

Detecting vehicles from aerial footage presents unique challenges because vehicles appear different from the perspective of conventional ground-level images.

This project uses a pretrained aerial vehicle detection model to identify vehicles from overhead video footage and estimate their approximate distance using bounding-box dimensions and camera calibration.

The implementation runs in Google Colab and processes recorded video frame by frame.
<img width="323" height="581" alt="download (1)" src="https://github.com/user-attachments/assets/ca976f35-8518-443f-85c6-862352ef03f6" />
<img width="996" height="470" alt="download (3)" src="https://github.com/user-attachments/assets/3bf94d4d-0030-4ec9-aa66-5561103ab2df" />


## Features

- **Aerial Vehicle Detection:** Detects vehicles in overhead video footage.
- **Multi-Class Classification:** Identifies cars, vans, trucks, buses, and motorcycles (model class: `motor`).
- **Bounding Box Visualization:** Draws bounding boxes around detected vehicles.
- **Confidence Scores:** Displays model confidence for each detection.
- **Distance Estimation:** Estimates approximate camera-to-vehicle distance using monocular camera calibration.
- **Video Processing:** Processes recorded video frame by frame.
- **CSV Export:** Saves detection information, confidence scores, bounding-box coordinates, and estimated distances.
- **Visual Analysis:** Displays detection results and supports further analysis of detection data.

## Technology Stack

| Technology | Purpose |
|---|---|
| Python | Main programming language |
| YOLOv8 | Pretrained aerial vehicle detection |
| Ultralytics | Object detection and inference |
| OpenCV | Video processing and visualization |
| NumPy | Numerical computations |
| Pandas | Detection data management and CSV export |
| Matplotlib | Visualization and analysis |
| Google Colab | Development and execution environment |
| Hugging Face Hub | Pretrained model weight retrieval |

## Pretrained Model

This project uses a pretrained aerial vehicle detection model:

**DroneFlow YOLOv8n VisDrone Vehicles**

Model repository:
https://huggingface.co/sobucki/droneflow-yolov8n-visdrone-vehicles

The model weights are downloaded from the repository's `weights/best.pt` file.

### Supported Classes

| Class ID | Vehicle Category |
|---|---|
| 0 | Car |
| 1 | Van |
| 2 | Truck |
| 3 | Bus |
| 4 | Motor |

Unlike general-purpose object detection models trained on everyday images, this model is intended for aerial vehicle detection.

## System Workflow

1. **Video Input:** Upload a recorded aerial video.
2. **Video Initialization:** Read video properties such as FPS, frame dimensions, and total frame count.
3. **Model Loading:** Download and load the pretrained aerial vehicle detection model.
4. **Frame Extraction:** Read the video frame by frame using OpenCV.
5. **Vehicle Detection:** Apply YOLO inference to identify vehicles.
6. **Classification:** Retrieve the predicted vehicle category and confidence score.
7. **Distance Estimation:** Calculate approximate distance for the calibrated vehicle category.
8. **Annotation:** Draw bounding boxes and detection information on video frames.
9. **Data Export:** Save detection records into a CSV file.
10. **Output Generation:** Export the annotated video and detection dataset.

## Distance Estimation Methodology

The project uses monocular camera calibration to estimate the approximate distance between the camera and a detected vehicle.

The method uses the relationship between an object's real-world width, its apparent width in pixels, and the camera's focal length.

### Camera Calibration

A reference vehicle or object with a known real-world width is placed at a known distance from the camera.

The focal length is estimated using:

\[
f = \frac{w \times D}{W}
\]

Where:

- \(f\) = Estimated focal length in pixels
- \(w\) = Width of the reference object in pixels
- \(D\) = Known distance from the camera
- \(W\) = Actual width of the reference object

### Distance Calculation

After calibration, the approximate distance to a detected vehicle is calculated using:

\[
D = \frac{W \times f}{w}
\]

Where:

- \(D\) = Estimated distance
- \(W\) = Assumed real-world vehicle width
- \(f\) = Calibrated focal length
- \(w\) = Detected bounding-box width in pixels

**Note:** Distance estimation is approximate and depends on correct calibration, assumed vehicle dimensions, camera orientation, perspective, and bounding-box accuracy.

## Project Outputs

The system generates two primary outputs:

### 1. Annotated Video

`object_detection_distance.mp4`

Contains:
- Detected vehicles
- Bounding boxes
- Predicted vehicle categories
- Detection confidence scores
- Estimated distances for calibrated vehicles

### 2. Detection Dataset

`detection_results.csv`

Contains the following information:

| Column | Description |
|---|---|
| frame | Video frame number |
| timestamp_seconds | Frame timestamp |
| object | Predicted vehicle category |
| confidence | Detection confidence |
| pixel_width | Bounding-box width in pixels |
| distance_meters | Estimated distance in meters |
| x1, y1 | Top-left bounding-box coordinates |
| x2, y2 | Bottom-right bounding-box coordinates |

## Installation and Execution

The project is implemented in Google Colab.

### 1. Install Dependencies

```python
!pip install -q ultralytics huggingface_hub opencv-python-headless pandas matplotlib
```

### 2. Import Libraries

```python
import cv2
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from ultralytics import YOLO
from huggingface_hub import hf_hub_download
```

### 3. Load the Pretrained Model

```python
weights_path = hf_hub_download(
    repo_id="sobucki/droneflow-yolov8n-visdrone-vehicles",
    filename="weights/best.pt"
)

model = YOLO(weights_path)

print(model.names)
```

### 4. Upload Video

```python
from google.colab import files

uploaded = files.upload()
video_path = next(iter(uploaded))
```

### 5. Run Detection and Distance Estimation

Use the video-processing and camera-calibration cells provided in the project notebook to process the uploaded video.

### 6. Export Results

The system generates:

- `object_detection_distance.mp4`
- `detection_results.csv`

Download the CSV and annotated video using:

```python
from google.colab import files

files.download("detection_results.csv")
files.download("object_detection_distance.mp4")
```

## Limitations

- Distance estimates depend on accurate camera calibration and assumed real-world vehicle dimensions.
- Vehicle orientation can affect bounding-box width and consequently distance calculations.
- Camera movement, perspective distortion, and changes in zoom can reduce estimation reliability.
- Small or partially occluded vehicles may be difficult to detect.
- Detection confidence does not guarantee correct classification.
- The pretrained model's performance may vary depending on video resolution, lighting, camera angle, and environmental conditions.
- The current implementation processes recorded video and does not provide real-time autonomous navigation or collision avoidance.

## Future Improvements

- Integrate vehicle tracking to maintain consistent vehicle IDs across frames.
- Improve distance estimation using depth estimation or additional camera calibration techniques.
- Evaluate detection performance using precision, recall, and mAP.
- Improve bounding-box accuracy and handling of partially occluded vehicles.
- Extend the system to support real-time aerial video processing.
- Explore integration with autonomous robotic and UAV perception systems.

## Applications

Potential applications include:

- Aerial traffic monitoring
- Parking lot vehicle analysis
- Drone-based vehicle surveillance
- Autonomous vehicle perception research
- Robotics and UAV vision systems

## License and Attribution

This project uses pretrained model weights from the DroneFlow YOLOv8n VisDrone Vehicles repository.

Please review the model repository's license, the Ultralytics licensing terms, and the dataset's usage restrictions before redistributing model weights or using the project commercially.

Model source:
https://huggingface.co/sobucki/droneflow-yolov8n-visdrone-vehicles

---

**Developed as a computer vision and robotics-oriented project exploring aerial vehicle detection and monocular distance estimation.**
