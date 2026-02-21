# Traffic Sign Detection with YOLO

A machine learning project implementing real-time traffic sign detection and recognition using YOLOv8 (Ultralytics) with GPS coordinate logging. This project uses computer vision to identify traffic signs from webcam feeds and maps their locations for navigation assistance.

## Project Overview

This repository contains Jupyter notebooks for training and deploying a traffic sign detection system that:
- Detects various traffic signs in real-time using webcam input
- Logs GPS coordinates of detected signs
- Visualizes sign locations on interactive maps using Folium
- Supports custom model training with Roboflow datasets

## Features

- 🚦 **Real-Time Detection** - Live traffic sign detection from webcam feed
- 🗺️ **GPS Integration** - Logs geographic coordinates of detected signs
- 📊 **Custom Training** - Train YOLOv8 models on custom traffic sign datasets
- 🎯 **High Accuracy** - Utilizes state-of-the-art YOLO object detection
- 📍 **Interactive Mapping** - Visualize detected signs on Folium maps
- 🔄 **Dataset Management** - Integration with Roboflow for dataset versioning
- 📷 **OCR Support** - Optional text recognition with Tesseract OCR
- 📝 **Comprehensive Logging** - Stores detection data with timestamps and locations

## Technologies Used

### Machine Learning & Computer Vision
- **YOLOv8** (Ultralytics) - Object detection model
- **YOLOv3** - Alternative YOLO implementation
- **OpenCV (cv2)** - Image processing and video capture
- **NumPy** - Numerical computations

### Data & Training
- **Roboflow** - Dataset management and versioning
- **PyTorch** - Deep learning framework (backend for YOLO)

### Additional Tools
- **Tesseract OCR** (pytesseract) - Optical character recognition
- **Folium** - Interactive map visualization
- **PyAutoGUI** - GUI automation support

### Development Environment
- **Jupyter Notebook** - Interactive development
- **Python 3.12+** - Programming language

## Repository Structure

```
MLJupyter/
├── TrafficSignsPI.ipynb          # Main detection with GPS logging
├── trainTrafficSigns.ipynb       # Model training notebook
├── webcamDetect.ipynb            # Webcam detection implementation
├── Untitled.ipynb                # Experimental notebook
├── Untitled1.ipynb               # Experimental notebook
├── SandBox-1/                    # Training dataset
│   ├── train/                    # Training images and labels
│   │   ├── images/               # Training images
│   │   └── labels/               # YOLO format annotations
│   ├── valid/                    # Validation set
│   ├── test/                     # Test set
│   └── data.yaml                 # Dataset configuration
├── yolov3.weights                # Pre-trained YOLO weights
├── yolov3.cfg                    # YOLO configuration
└── runs/                         # Training run outputs
```

## Installation

### Prerequisites

1. **Python 3.12+** installed
2. **Jupyter Notebook** or **JupyterLab**
3. **CUDA-capable GPU** (optional, for faster training)
4. **Tesseract OCR** (optional, for text recognition)

### Setup Instructions

1. **Clone the Repository**
   ```bash
   git clone https://github.com/zchalmers/MLJupyter.git
   cd MLJupyter
   ```

2. **Create a Virtual Environment** (recommended)
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install Required Packages**
   ```bash
   pip install ultralytics opencv-python numpy roboflow folium pytesseract pyautogui jupyter
   ```

4. **Install Tesseract OCR** (Optional)
   - **Windows**: Download from [GitHub Tesseract](https://github.com/UB-Mannheim/tesseract/wiki)
   - **macOS**: `brew install tesseract`
   - **Linux**: `sudo apt-get install tesseract-ocr`

5. **Download YOLO Weights** (if not included)
   ```bash
   # For YOLOv3
   wget https://pjreddie.com/media/files/yolov3.weights
   
   # YOLOv8 weights will be downloaded automatically by Ultralytics
   ```

6. **Set up Roboflow** (for custom training)
   - Sign up at [Roboflow](https://roboflow.com/)
   - Create or import a traffic sign dataset
   - Get your API key from account settings

## Usage

### 1. Training a Custom Model

Open and run `trainTrafficSigns.ipynb`:

```python
# Configure your Roboflow API key
rf = Roboflow(api_key="YOUR_API_KEY_HERE")

# Download your dataset
project = rf.workspace("your-workspace").project("your-project")
version = project.version(1)
dataset = version.download("yolov8")

# Train the model
from ultralytics import YOLO
model = YOLO('yolov8n.pt')
results = model.train(data=f'{dataset.location}/data.yaml', epochs=10, batch=16)
```

### 2. Real-Time Detection with GPS Logging

Open and run `TrafficSignsPI.ipynb`:

```python
# The notebook will:
# 1. Initialize webcam capture
# 2. Load trained YOLO model
# 3. Detect traffic signs in real-time
# 4. Log GPS coordinates (simulated or from GPS device)
# 5. Display bounding boxes and labels
# 6. Generate interactive map with sign locations
```

### 3. Webcam Detection

Open and run `webcamDetect.ipynb` for a simpler webcam detection setup without GPS logging.

### Detected Traffic Sign Classes

The model can detect various traffic signs including:
- Stop signs
- Yield signs
- Speed limit signs
- Pedestrian crossing signs
- No entry signs
- Traffic lights
- And more (depending on training dataset)

## Configuration

### Tesseract Path (Windows)

If using OCR functionality, uncomment and set the Tesseract path in the notebooks:

```python
pytesseract.pytesseract.tesseract_cmd = r"C:\Program Files\Tesseract-OCR\tesseract.exe"
```

### GPS Configuration

The current implementation uses simulated GPS data. To use real GPS:

1. **GPS Module**: Connect a GPS sensor (e.g., via serial/USB)
2. **GPS Library**: Install `gpsd` or `pynmea2`
3. **Modify GPS Function**:
   ```python
   import gpsd
   
   def get_gps_data():
       packet = gpsd.get_current()
       return {
           "latitude": packet.lat,
           "longitude": packet.lon
       }
   ```

### Model Configuration

Adjust detection parameters in the notebooks:

```python
# Confidence threshold
confidence_threshold = 0.5

# NMS (Non-Maximum Suppression) threshold
nms_threshold = 0.4

# Input image size
img_size = 640  # YOLOv8 default
```

## Training Parameters

Customize training in `trainTrafficSigns.ipynb`:

```python
results = model.train(
    data='path/to/data.yaml',
    epochs=10,          # Number of training epochs
    batch=16,           # Batch size
    imgsz=640,          # Image size
    device='cpu',       # 'cpu' or 'cuda' for GPU
    workers=8,          # Number of workers
    patience=100,       # Early stopping patience
)
```

## Output

The system generates:
- **Real-time video** with bounding boxes and labels
- **GPS log** (list of dictionaries with coordinates and labels)
- **Interactive map** (Folium HTML) showing detected sign locations
- **Training metrics** (loss, precision, recall, mAP)
- **Model weights** saved in `runs/detect/train*/weights/`

## Troubleshooting

### Common Issues

**"YOLO weights not found"**
- Download YOLOv3 weights manually or let Ultralytics download YOLOv8 automatically
- Ensure weights file is in the correct directory

**"Tesseract not found"**
- Install Tesseract OCR and set the correct path
- Comment out Tesseract-related code if not using OCR

**"CUDA out of memory"**
- Reduce batch size: `batch=8` or `batch=4`
- Use smaller image size: `imgsz=416`
- Use CPU instead: `device='cpu'`

**"Dataset not found"**
- Verify Roboflow API key is correct
- Check internet connection
- Ensure dataset has been properly exported in YOLOv8 format

**Webcam not opening**
- Check camera permissions
- Try different camera index: `cv2.VideoCapture(1)` instead of `0`
- Verify camera is not in use by another application

**Low detection accuracy**
- Train for more epochs
- Use a larger model: `yolov8s.pt` or `yolov8m.pt`
- Increase dataset size and diversity
- Adjust confidence threshold

## Performance Optimization

### For Faster Training
- Use GPU: `device='cuda'`
- Increase batch size (if memory allows)
- Use mixed precision: `amp=True`
- Enable caching: `cache=True`

### For Real-Time Detection
- Use smaller model: `yolov8n.pt` (nano)
- Reduce input size: `imgsz=416`
- Lower FPS if needed
- Use GPU inference

## Future Enhancements

- [ ] Add support for more traffic sign classes
- [ ] Implement real GPS device integration
- [ ] Add speed limit text recognition with OCR
- [ ] Create mobile app deployment
- [ ] Add cloud-based model serving
- [ ] Implement sign tracking across frames
- [ ] Add night vision / low-light detection
- [ ] Create dashboard for analytics

## Dataset Information

This project uses traffic sign datasets from Roboflow. The SandBox-1 dataset includes:
- Training images with bounding box annotations
- Validation and test splits
- YOLO format labels (.txt files)
- Various traffic sign types from different angles and lighting conditions

## Contributing

Feel free to fork this repository and submit pull requests for improvements!

## License

This project is available for educational and research purposes.

## Acknowledgments

- **Ultralytics** - For the YOLOv8 framework
- **Roboflow** - For dataset management tools
- **OpenCV** - For computer vision capabilities
- **Folium** - For interactive mapping

## Contact

For questions or collaboration:
- **GitHub**: [@zchalmers](https://github.com/zchalmers)

## References

- [YOLOv8 Documentation](https://docs.ultralytics.com/)
- [Roboflow Documentation](https://docs.roboflow.com/)
- [OpenCV Documentation](https://docs.opencv.org/)
- [Traffic Sign Detection Papers](https://paperswithcode.com/task/traffic-sign-recognition)
