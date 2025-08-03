# 🔗 Camera-Model Integration Guide

## 📋 Overview

This document explains how camera configurations from the frontend are properly passed to the detection models in Sakshi.Ai. The system ensures that RTSP links, camera names, and model settings are correctly connected between the frontend, backend, and detection systems.

## 🔄 Complete Integration Flow

### 1. Frontend to Backend Communication

```mermaid
sequenceDiagram
    participant F as Frontend
    participant B as Backend API
    participant DB as Database
    participant M as Detection Models

    F->>B: POST /api/cameras
    Note over F,B: Camera name, RTSP URL, apps, model_config
    B->>DB: Store camera configuration
    B->>M: Update cameras.json for each app
    B->>F: Return camera configuration
```

### 2. Backend to Detection Models

```mermaid
sequenceDiagram
    participant B as Backend API
    participant CF as Camera Manager
    participant DM as Detection Models
    participant Y as YOLO Models

    B->>CF: update_app_cameras_config()
    CF->>CF: Create cameras.json
    CF->>DM: Load camera configurations
    DM->>Y: Load YOLO models with settings
    DM->>CF: Process camera streams
```

## 🏗️ Architecture Components

### 1. Frontend Camera Management

The frontend allows users to:
- **Add cameras** with RTSP URLs, names, and locations
- **Select detection apps** for each camera (cash_detector, theft_detector, monitor_detector)
- **Configure model settings** (confidence threshold, frame skip)
- **Test camera connections** before deployment

```javascript
// Frontend camera creation
const cameraData = {
  name: "Security Camera 1",
  rtsp_url: "rtsp://admin:password@192.168.1.100:554/stream1",
  location: "Main Entrance",
  enabled: true,
  apps: ["cash_detector", "theft_detector"],
  model_config: {
    confidence_threshold: 0.6,
    frame_skip: 3
  }
};

const response = await fetch('/api/cameras', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(cameraData)
});
```

### 2. Backend API Integration

The backend provides comprehensive APIs for camera-model integration:

#### Camera Management APIs
```python
# Add camera with model integration
@app.post("/api/cameras")
def add_camera(camera: CameraConfig):
    # Validate camera configuration
    # Store in database
    # Update all associated apps
    # Create cameras.json for each app

# Update camera configuration
@app.put("/api/cameras/{name}")
def update_camera(name: str, camera: CameraConfig):
    # Update camera properties
    # Update database
    # Update all associated apps

# Delete camera
@app.delete("/api/cameras/{name}")
def delete_camera(name: str):
    # Remove camera from store
    # Update all affected apps
```

#### Model Configuration APIs
```python
# Get model information
@app.get("/api/apps/{app_name}/model-info")
def get_app_model_info(app_name: str):
    # Return model path, classes, settings

# Start app with camera validation
@app.post("/api/apps/{app_name}/start")
def start_app(app_name: str, background_tasks: BackgroundTasks):
    # Validate model exists
    # Get cameras for this app
    # Update camera configuration
    # Start detection container
```

#### Testing and Monitoring APIs
```python
# Test camera RTSP connection
@app.post("/api/cameras/{name}/test-connection")
def test_camera_connection(name: str):
    # Test RTSP stream connection
    # Verify frame reading

# Get comprehensive system status
@app.get("/api/system/camera-model-status")
def get_camera_model_status():
    # Return all cameras and their model connections
```

### 3. Detection Model Integration

Each detection model reads camera configurations from `cameras.json`:

#### Cash Detection Model
```python
class MultiCameraCashDetector:
    def setup_model(self):
        # Get model settings from camera manager
        settings = self.camera_manager.get_settings()
        model_path = settings.get('model_path', MODEL_PATH)
        confidence_threshold = settings.get('confidence_threshold', CONFIDENCE_THRESHOLD)
        
        self.model = YOLO(model_path)
        self.confidence_threshold = confidence_threshold

    def process_camera(self, camera_id):
        # Get frame skip from settings
        settings = self.camera_manager.get_settings()
        frame_skip = settings.get('frame_skip', FRAME_SKIP)
        
        # Process camera with model settings
        if self.frame_counters[camera_id] % frame_skip == 0:
            detections, processing_time = self.detect_cash(frame)
```

#### Theft Detection Model
```python
class TheftAppDetector:
    def setup_cameras(self):
        # Load camera configuration from JSON file
        cameras_config_path = 'cameras.json'
        with open(cameras_config_path, 'r') as f:
            config = json.load(f)
        
        self.cameras = config.get('cameras', [])
        self.camera_settings = config.get('settings', {})
        
        # Filter enabled cameras
        self.enabled_cameras = [cam for cam in self.cameras if cam.get('enabled', True)]
```

#### Monitor Detection Model
```python
class MonitorDetector:
    def load_cameras(self):
        # Load camera configurations
        with open('cameras.json', 'r') as f:
            config = json.load(f)
            return config.get('cameras', [])
```

## 📁 Configuration Files

### 1. cameras.json Structure

Each detection app has its own `cameras.json` file:

```json
{
  "cameras": [
    {
      "id": "Security Camera 1",
      "name": "Security Camera 1",
      "location": "Main Entrance",
      "rtsp_url": "rtsp://admin:password@192.168.1.100:554/stream1",
      "enabled": true,
      "model_config": {
        "model_path": "/app/Cash_Detection_Docker/27-july-2025.pt",
        "model_type": "yolo",
        "classes": ["cash", "bill", "money"],
        "confidence_threshold": 0.55,
        "frame_skip": 3
      }
    }
  ],
  "settings": {
    "frame_skip": 3,
    "confidence_threshold": 0.55,
    "recording_duration": 10,
    "max_cameras": 1,
    "model_path": "/app/Cash_Detection_Docker/27-july-2025.pt",
    "model_type": "yolo",
    "classes": ["cash", "bill", "money"]
  }
}
```

### 2. Model Configuration

The backend maintains model configurations:

```python
MODEL_CONFIGS = {
    'cash_detector': {
        'model_path': '/app/Cash_Detection_Docker/27-july-2025.pt',
        'model_type': 'yolo',
        'classes': ['cash', 'bill', 'money'],
        'confidence_threshold': 0.55,
        'frame_skip': 3
    },
    'theft_detector': {
        'model_path': '/app/yolo-pose-shoplifting/models/best.pt',
        'model_type': 'yolo_pose',
        'classes': ['person', 'shoplifting', 'suspicious'],
        'confidence_threshold': 0.6,
        'frame_skip': 2
    },
    'monitor_detector': {
        'model_path': '/app/monitor_detection_system/monitor_model.pt',
        'model_type': 'custom',
        'classes': ['monitor_on', 'monitor_off'],
        'confidence_threshold': 0.7,
        'frame_skip': 5
    }
}
```

## 🔧 Integration Process

### 1. Camera Addition Process

```python
def add_camera_with_model_integration(camera: CameraConfig):
    """Complete process of adding camera with model integration"""
    
    # 1. Validate camera configuration
    if not camera.name or not camera.rtsp_url:
        raise ValueError("Camera name and RTSP URL are required")
    
    # 2. Store camera in backend
    camera_store.append(camera)
    update_camera_in_db(camera)
    
    # 3. Update all associated apps
    for app_name in camera.apps:
        if app_name in APP_DIRS:
            # Get all cameras for this app
            app_cameras = [cam.name for cam in camera_store 
                          if app_name in cam.apps and cam.enabled]
            
            # Update camera configuration for the app
            update_app_cameras_config(app_name, app_cameras)
    
    return camera
```

### 2. App Start Process

```python
def start_app_with_camera_validation(app_name: str):
    """Start detection app with camera validation"""
    
    # 1. Validate model exists
    if not validate_model_path(app_name):
        raise ValueError(f"Model not found for {app_name}")
    
    # 2. Get cameras for this app
    app_cameras = [cam.name for cam in camera_store 
                   if app_name in cam.apps and cam.enabled]
    
    if not app_cameras:
        raise ValueError(f"No cameras configured for {app_name}")
    
    # 3. Update camera configuration
    update_app_cameras_config(app_name, app_cameras)
    
    # 4. Start detection container
    start_detection_container(app_name)
    
    return {
        "app_name": app_name,
        "cameras": app_cameras,
        "model_info": get_model_info(app_name)
    }
```

### 3. Detection Model Loading

```python
def load_detection_model_with_cameras(app_name: str):
    """Load detection model with camera configurations"""
    
    # 1. Read camera configuration
    cameras_file = f"{APP_DIRS[app_name]}/cameras.json"
    with open(cameras_file, 'r') as f:
        config = json.load(f)
    
    # 2. Load model with settings
    settings = config['settings']
    model_path = settings['model_path']
    confidence_threshold = settings['confidence_threshold']
    
    model = YOLO(model_path)
    
    # 3. Initialize camera streams
    cameras = config['cameras']
    for camera in cameras:
        if camera['enabled']:
            initialize_camera_stream(camera)
    
    return model, cameras, settings
```

## 🧪 Testing Integration

### 1. Manual Testing

```bash
# Test camera creation
curl -X POST http://localhost:5000/api/cameras \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Test Camera",
    "rtsp_url": "rtsp://test:test@192.168.1.100:554/stream1",
    "location": "Test Location",
    "enabled": true,
    "apps": ["cash_detector"],
    "model_config": {}
  }'

# Test camera connection
curl -X POST http://localhost:5000/api/cameras/Test%20Camera/test-connection

# Test app camera configuration
curl http://localhost:5000/api/apps/cash_detector/camera-config

# Test app start with cameras
curl -X POST http://localhost:5000/api/apps/cash_detector/start
```

### 2. Automated Testing

Run the comprehensive integration test:

```bash
python test_camera_model_integration.py
```

This test verifies:
- ✅ Frontend camera entries are stored in backend
- ✅ Camera configurations are passed to detection models
- ✅ Detection models read camera configurations
- ✅ Model settings are applied correctly
- ✅ RTSP connections are established
- ✅ Apps can be started with camera configurations

## 📊 Monitoring and Debugging

### 1. System Status API

```bash
curl http://localhost:5000/api/system/camera-model-status
```

Returns comprehensive status including:
- Total cameras and enabled cameras
- App status and associated cameras
- Model configurations and existence
- Camera details with RTSP URLs

### 2. Log Monitoring

```bash
# Backend logs
tail -f backend/logs/app.log

# Detection model logs
tail -f Cash_Detection_Docker/cash_detection.log
tail -f yolo-pose-shoplifting/logs/theftapp_detector.log
tail -f monitor_detection_system/logs/monitor_detector.log
```

### 3. Configuration Verification

```bash
# Check cameras.json files
ls -la */cameras.json

# Verify model files
ls -la */models/*.pt

# Check database entries
psql -h localhost -U sakshi_user -d sakshi_ai_db -c "SELECT * FROM cameras;"
```

## 🚨 Troubleshooting

### 1. Common Issues

#### Camera Connection Failed
```bash
# Test RTSP connection manually
ffmpeg -i "rtsp://admin:password@192.168.1.100:554/stream1" -t 5 -f null -

# Check network connectivity
ping 192.168.1.100

# Verify camera credentials
curl -u admin:password http://192.168.1.100:80
```

#### Model Loading Failed
```bash
# Check model file exists
ls -la Cash_Detection_Docker/27-july-2025.pt

# Validate model path
curl http://localhost:5000/api/models/available

# Test model loading
python -c "from ultralytics import YOLO; model = YOLO('Cash_Detection_Docker/27-july-2025.pt')"
```

#### App Start Failed
```bash
# Check app directory exists
ls -la Cash_Detection_Docker/

# Verify cameras.json exists
cat Cash_Detection_Docker/cameras.json

# Check app logs
docker logs cash_detector_container
```

### 2. Debug Commands

```bash
# Get detailed camera config
curl http://localhost:5000/api/cameras/Test%20Camera/config

# Test all cameras for an app
curl -X POST http://localhost:5000/api/apps/cash_detector/test-cameras

# Force update camera config
curl -X POST http://localhost:5000/api/apps/cash_detector/update-camera-config
```

## 🎯 Best Practices

### 1. Camera Configuration
- **Use descriptive camera names** for easy identification
- **Test RTSP connections** before adding to system
- **Group cameras by location** for better organization
- **Set appropriate confidence thresholds** for each use case

### 2. Model Integration
- **Validate model files** before deployment
- **Monitor model performance** and adjust settings
- **Use appropriate frame skip** for performance optimization
- **Test with sample videos** before live deployment

### 3. System Monitoring
- **Monitor camera connections** regularly
- **Check detection accuracy** and adjust thresholds
- **Monitor system resources** (CPU, GPU, memory)
- **Set up alerts** for system failures

## 📈 Performance Optimization

### 1. Frame Processing
```python
# Optimize frame skip based on performance
frame_skip = 3  # Process every 3rd frame
confidence_threshold = 0.6  # Adjust based on accuracy needs

# Monitor processing time
processing_times = []
avg_time = np.mean(processing_times)
if avg_time > 0.1:  # 100ms threshold
    frame_skip += 1  # Increase frame skip
```

### 2. Memory Management
```python
# Clear GPU memory periodically
if torch.cuda.is_available():
    torch.cuda.empty_cache()

# Limit camera buffer size
cap.set(cv2.CAP_PROP_BUFFERSIZE, 1)
```

### 3. Network Optimization
```python
# Set RTSP timeout
cap.set(cv2.CAP_PROP_OPEN_TIMEOUT_MSEC, 5000)
cap.set(cv2.CAP_PROP_READ_TIMEOUT_MSEC, 5000)

# Use TCP for RTSP (more reliable)
rtsp_url = rtsp_url.replace("rtsp://", "rtspt://")
```

---

**Camera-Model Integration** - Ensuring seamless connection between frontend camera configurations and detection models 🚀

This integration ensures that all camera configurations from the frontend are properly passed to the detection models, enabling real-time monitoring with the correct RTSP streams and model settings. 
