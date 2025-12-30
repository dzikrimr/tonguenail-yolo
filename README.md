# YOLO Tongue & Nail Detection API

A high-performance REST API service for detecting **nails (kuku)** and **tongues (lidah)** in images using state-of-the-art YOLOv8 object detection models. Built with FastAPI and deployed with Docker for easy scalability and deployment.

## ✨ Features

- 🚀 **High-Performance Detection**: Powered by YOLOv8 for accurate object detection
- 🔧 **Dedicated Endpoints**: Separate optimized endpoints for nails and tongues
- 📊 **Confidence Scoring**: Returns confidence scores for each detection
- 🌐 **RESTful API**: Clean, FastAPI-based REST interface
- 🐳 **Containerized**: Docker support for easy deployment
- ⚡ **Async Support**: Asynchronous request handling for better performance
- 📏 **Normalized Coordinates**: Returns bounding box coordinates in normalized format
- 🛡️ **Error Handling**: Comprehensive error handling and validation

## 🏗️ Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Client App    │───▶│   FastAPI Server │───▶│  YOLOv8 Models  │
│  (Image Upload) │    │   (main.py)      │    │ (kuku & lidah)  │
└─────────────────┘    └──────────────────┘    └─────────────────┘
         │                       │                       │
         │                       ▼                       │
         │              ┌───────────────┐               │
         └──────────────▶│   Response    │◀──────────────┘
                        │ (Detection +  │
                        │  Confidence)  │
                        └───────────────┘
```

## 🚀 Quick Start

### Prerequisites

- Python 3.11+
- Docker (optional)
- YOLOv8 model files (`.pt` format)

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd yolo_tongue_nail_service
   ```

2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Prepare model files**
   - Place your trained YOLOv8 models in the `models/` directory:
     - `models/kuku_best.pt` - for nail detection
     - `models/lidah_best.pt` - for tongue detection

4. **Run the application**
   ```bash
   python main.py
   ```

### Docker Deployment

1. **Build the Docker image**
   ```bash
   docker build -t yolo-detection-api .
   ```

2. **Run the container**
   ```bash
   docker run -p 8000:8000 -v $(pwd)/models:/app/models yolo-detection-api
   ```

## 📚 API Documentation

### Base URL
```
http://localhost:8000
```

### Endpoints

#### 1. Health Check
```http
GET /
```

**Response:**
```json
{
  "message": "Welcome to the YOLO Dedicated Detection API!"
}
```

#### 2. Nail Detection
```http
POST /detect/kuku
Content-Type: multipart/form-data
```

**Request Body:**
- `file`: Image file (JPEG, PNG, etc.)

**Response:**
```json
{
  "detection": {
    "class_name": "kuku",
    "confidence": 0.89,
    "box_normalized": [0.15, 0.23, 0.67, 0.78]
  },
  "message": "Kuku terlihat"
}
```

#### 3. Tongue Detection
```http
POST /detect/lidah
Content-Type: multipart/form-data
```

**Request Body:**
- `file`: Image file (JPEG, PNG, etc.)

**Response:**
```json
{
  "detection": {
    "class_name": "lidah",
    "confidence": 0.92,
    "box_normalized": [0.12, 0.31, 0.71, 0.85]
  },
  "message": "Lidah terlihat"
}
```

### Response Format

Each detection response contains:
- **`detection`**: Object containing detection details or `null` if no object detected
  - **`class_name`**: The detected class name ("kuku" or "lidah")
  - **`confidence`**: Confidence score (0-1)
  - **`box_normalized`**: Bounding box coordinates in normalized format `[x1, y1, x2, y2]`
- **`message`**: Descriptive message in Indonesian

## 🧪 Testing the API

### Using curl

**Test nail detection:**
```bash
curl -X POST "http://localhost:8000/detect/kuku" \
     -H "accept: application/json" \
     -H "Content-Type: multipart/form-data" \
     -F "file=@/path/to/your/image.jpg"
```

**Test tongue detection:**
```bash
curl -X POST "http://localhost:8000/detect/lidah" \
     -H "accept: application/json" \
     -H "Content-Type: multipart/form-data" \
     -F "file=@/path/to/your/image.jpg"
```

### Using Python requests

```python
import requests

# Test nail detection
with open("test_image.jpg", "rb") as f:
    response = requests.post(
        "http://localhost:8000/detect/kuku",
        files={"file": f}
    )
    print(response.json())
```

## 📁 Project Structure

```
yolo_tongue_nail_service/
├── main.py                 # FastAPI application with detection endpoints
├── requirements.txt        # Python dependencies
├── Dockerfile             # Docker containerization
├── .gitignore            # Git ignore rules
├── models/               # YOLOv8 model files
│   ├── kuku_best.pt     # Nail detection model
│   └── lidah_best.pt    # Tongue detection model
└── README.md            # This file
```

## 🔧 Configuration

### Confidence Threshold
The default confidence threshold is set to **0.5** (50%). You can modify this in `main.py`:

```python
CONFIDENCE_THRESHOLD = 0.5  # Adjust as needed
```

### Model Paths
Model files are loaded from:
- Nail model: `models/kuku_best.pt`
- Tongue model: `models/lidah_best.pt`

## 🚀 Deployment Options

### 1. Local Development
```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### 2. Production with Gunicorn
```bash
gunicorn main:app -w 4 -k uvicorn.workers.UvicornWorker --bind 0.0.0.0:8000
```

### 3. Docker Compose (Optional)
Create `docker-compose.yml`:
```yaml
version: '3.8'
services:
  api:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - ./models:/app/models
    environment:
      - MODEL_KUKU_PATH=models/kuku_best.pt
      - MODEL_LIDAH_PATH=models/lidah_best.pt
```

## 🔍 Model Information

### Training Requirements
- **Format**: YOLOv8 PyTorch format (.pt)
- **Classes**: "kuku" (nails), "lidah" (tongues)
- **Input Size**: Automatically handled by YOLOv8
- **Confidence Threshold**: 0.5 (adjustable)

### Performance Optimization
- Models are loaded once at startup for better performance
- Asynchronous request handling for concurrent requests
- Memory-efficient image processing with PIL

## 🛠️ Development

### Adding New Detection Models

1. Train your YOLOv8 model following [Ultralytics documentation](https://docs.ultralytics.com/)
2. Place the `.pt` file in the `models/` directory
3. Update `main.py` to load the new model
4. Add a new endpoint following the existing pattern

### Customizing Detection Logic

Modify the `get_best_detection_from_result()` function to:
- Adjust confidence thresholds
- Filter specific classes
- Return additional metadata
- Implement custom post-processing

## 📊 Monitoring & Logging

The application includes basic logging for:
- Model loading status
- Request processing
- Error handling

To enhance monitoring, consider adding:
- Prometheus metrics
- Structured logging (JSON format)
- Health check endpoints
- Request/response logging
