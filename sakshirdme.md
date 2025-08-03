# 🚀 Sakshi.Ai - AI-Powered Surveillance System

## 📋 Overview

Sakshi.Ai is a comprehensive AI-powered surveillance system that integrates multiple YOLO models for cash detection, theft detection, and monitor status monitoring. The system features real-time video processing, multi-threading architecture, and seamless frontend-backend communication.

## 🏗️ System Architecture

### Complete System Flow

```mermaid
graph TB
    subgraph "Frontend (React)"
        A[React App] --> B[Camera Management]
        A --> C[Live Streams]
        A --> D[App Status Dashboard]
        A --> E[Analytics Dashboard]
    end
    
    subgraph "Backend API (FastAPI)"
        F[FastAPI Backend] --> G[Camera API]
        F --> H[Model Management API]
        F --> I[Detection API]
        F --> J[Database API]
        F --> K[Streaming API]
    end
    
    subgraph "Threading Architecture"
        L[Main Thread] --> M[Background Tasks]
        L --> N[Detection Threads]
        L --> O[Alert Threads]
        L --> P[Database Threads]
    end
    
    subgraph "YOLO Model Integration"
        Q[Cash Detection Model] --> R[YOLO: 27-july-2025.pt]
        S[Theft Detection Model] --> T[YOLO Pose: best.pt]
        U[Monitor Detection Model] --> V[Custom Model: monitor_model.pt]
    end
    
    subgraph "Detection Systems"
        W[Cash Detector Container] --> X[Multi-threaded Processing]
        Y[Theft Detector Container] --> Z[Pose Analysis]
        AA[Monitor Detector Container] --> BB[Status Monitoring]
    end
    
    subgraph "Database & Storage"
        CC[PostgreSQL Database] --> DD[Detection Logs]
        CC --> EE[Analytics Data]
        CC --> FF[Camera Configurations]
    end
    
    A --> F
    F --> L
    Q --> W
    S --> Y
    U --> AA
    W --> CC
    Y --> CC
    AA --> CC
```

### Camera-Model Integration Architecture

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

## 🔗 Camera-Model Integration

### Overview

The system implements a sophisticated camera-model integration that ensures seamless communication between frontend camera configurations and detection models. This integration enables:

- **Real-time camera configuration** from frontend to detection models
- **Dynamic model settings** based on camera configurations
- **Automatic camera.json generation** for each detection app
- **RTSP stream validation** before deployment
- **Multi-app camera support** with individual configurations

### Integration Components

#### 1. Frontend Camera Management
```javascript
// Camera creation with model integration
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
```

#### 2. Backend Configuration Management
```python
# Camera configuration storage and validation
def add_camera_with_model_integration(camera: CameraConfig):
    # Validate camera configuration
    # Store in database
    # Update all associated apps
    # Create cameras.json for each app
    return camera

# Model configuration for each detection system
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

#### 3. Detection Model Integration
```python
# Each detection model reads camera configurations
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

### Configuration Files

#### cameras.json Structure
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

### Integration Process

#### 1. Camera Addition Process
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

#### 2. App Start Process
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

### Testing and Monitoring

#### System Status API
```bash
# Get comprehensive camera-model status
curl http://localhost:5000/api/system/camera-model-status

# Test camera connection
curl -X POST http://localhost:5000/api/cameras/Test%20Camera/test-connection

# Test app camera configuration
curl http://localhost:5000/api/apps/cash_detector/camera-config
```

#### Configuration Verification
```bash
# Check cameras.json files
ls -la */cameras.json

# Verify model files
ls -la */models/*.pt

# Check database entries
psql -h localhost -U sakshi_user -d sakshi_ai_db -c "SELECT * FROM cameras;"
```

## 🔄 Threading Architecture

### 1. Backend Threading Model

The backend implements a sophisticated multi-threading architecture for optimal performance:

#### Main Application Thread
```python
# Backend main thread handles API requests
@app.post("/api/apps/{app_name}/start")
def start_app(app_name: str, background_tasks: BackgroundTasks):
    # Validate model before starting
    if not validate_model_path(app_name):
        raise HTTPException(400, f"Model not found for {app_name}")
    
    # Start the app in background thread
    background_tasks.add_task(start_app_background, app_name, app_cameras)
```

#### Background Task Threads
```python
def start_app_background(app_name: str, cameras: List[str]):
    """Background thread for starting detection apps"""
    try:
        # Update camera configuration with model settings
        update_app_cameras_config(app_name, cameras)
        
        # Start detection container
        subprocess.run([
            "docker", "run", "-d",
            "--name", f"{app_name}_container",
            "--network", "sakshi_network",
            f"sakshi_{app_name}:latest"
        ])
        
        logger.info(f"Started {app_name} in background thread")
    except Exception as e:
        logger.error(f"Failed to start {app_name}: {e}")
```

### 2. Detection System Threading

Each detection system implements its own threading model:

#### Cash Detection Threading
```python
class MultiCameraCashDetector:
    def __init__(self):
        # Threading for better performance
        self.detection_threads = {}
        self.running = False
    
    def start_detection(self):
        """Start detection for all cameras"""
        self.running = True
        
        # Start camera manager
        self.camera_manager.start_all()
        
        # Start detection threads for each camera
        for camera_id in self.camera_manager.cameras.keys():
            thread = threading.Thread(
                target=self.process_camera, 
                args=(camera_id,), 
                daemon=True
            )
            thread.start()
            self.detection_threads[camera_id] = thread
```

#### Theft Detection Threading
```python
class TheftAppDetector:
    def __init__(self):
        self.suspicious_events = queue.Queue()
        self.detected_person_ids = set()
    
    def alert_worker(self):
        """Background thread for processing alerts"""
        while True:
            try:
                event = self.suspicious_events.get(timeout=1)
                asyncio.run(self.send_telegram_alert(**event))
            except queue.Empty:
                continue
            except Exception as e:
                logging.error(f"Alert worker error: {e}")
```

#### Monitor Detection Threading
```python
class MonitorDetector:
    def start_background_workers(self):
        """Start background threads for monitoring"""
        # Schedule daily reports
        schedule.every().day.at("18:00").do(daily_report)
        
        # Start scheduler in background thread
        def run_scheduler():
            while True:
                schedule.run_pending()
                time.sleep(60)
        
        scheduler_thread = threading.Thread(
            target=run_scheduler, 
            daemon=True
        )
        scheduler_thread.start()
```

### 3. Thread Communication Patterns

#### Queue-Based Communication
```python
# Thread-safe queue for inter-thread communication
self.suspicious_events = queue.Queue()

# Producer thread (detection)
def process_camera(self, camera_id):
    # ... detection logic ...
    if suspicious_activity:
        self.suspicious_events.put({
            'event_id': event_id,
            'confidence_score': confidence,
            'camera_id': camera_id
        })

# Consumer thread (alert worker)
def alert_worker(self):
    while True:
        event = self.suspicious_events.get()
        asyncio.run(self.send_telegram_alert(**event))
```

#### Shared State Management
```python
# Thread-safe shared state
self.detected_person_ids = set()
self.daily_counts = defaultdict(lambda: defaultdict(int))

# Thread-safe operations
def is_duplicate_detection(self, person_id, camera_id):
    key = f"{person_id}_{camera_id}"
    if key in self.detected_person_ids:
        return True
    self.detected_person_ids.add(key)
    return False
```

## 🌐 Frontend-Backend Communication

### 1. API Communication Flow

#### Real-time Data Fetching
```javascript
// Frontend periodic data fetching
useEffect(() => {
  fetchData();
  const interval = setInterval(fetchData, 5000); // Refresh every 5 seconds
  return () => clearInterval(interval);
}, []);

const fetchData = async () => {
  try {
    const [camerasRes, statusRes] = await Promise.all([
      fetch('http://localhost:8000/api/cameras'),
      fetch('http://localhost:8000/api/apps/status')
    ]);
    
    if (camerasRes.ok) {
      const camerasData = await camerasRes.json();
      setCameras(camerasData);
    }
    
    if (statusRes.ok) {
      const statusData = await statusRes.json();
      setAppStatuses(statusData);
    }
  } catch (error) {
    console.error('Error fetching data:', error);
  }
};
```

#### Model Selection Communication
```javascript
// Frontend model selection
const handleModelSelection = async (appName, modelConfig) => {
  try {
    const response = await fetch(`/api/apps/${appName}/start`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        model_config: modelConfig
      })
    });
    
    if (response.ok) {
      setSnackbar({ 
        open: true, 
        message: `${appName} started successfully`, 
        severity: 'success' 
      });
    }
  } catch (error) {
    console.error('Error starting app:', error);
  }
};
```

### 2. WebSocket Streaming

#### Real-time Video Streaming
```javascript
// Frontend video stream handling
const [streams, setStreams] = useState({});

const startStream = async (cameraName) => {
  try {
    const response = await fetch(`/api/stream/${cameraName}`);
    const blob = await response.blob();
    const url = URL.createObjectURL(blob);
    setStreams(prev => ({ ...prev, [cameraName]: url }));
  } catch (error) {
    console.error('Error starting stream:', error);
  }
};
```

#### Backend Streaming Response
```python
@app.get("/api/stream/{camera_name}")
def stream_camera(camera_name: str):
    """Stream camera feed"""
    def generate_frames():
        camera = next((cam for cam in camera_store if cam.name == camera_name), None)
        if not camera:
            return
        
        cap = cv2.VideoCapture(camera.rtsp_url)
        while True:
            ret, frame = cap.read()
            if not ret:
                break
            
            # Encode frame
            ret, buffer = cv2.imencode('.jpg', frame)
            if not ret:
                continue
            
            yield (b'--frame\r\n'
                   b'Content-Type: image/jpeg\r\n\r\n' + buffer.tobytes() + b'\r\n')
    
    return StreamingResponse(
        generate_frames(),
        media_type="multipart/x-mixed-replace; boundary=frame"
    )
```

### 3. State Synchronization

#### Camera Management
```javascript
// Frontend camera management
const handleSubmit = async () => {
  try {
    const url = editingCamera 
      ? `http://localhost:8000/api/cameras/${editingCamera.name}`
      : 'http://localhost:8000/api/cameras';
    
    const method = editingCamera ? 'PUT' : 'POST';
    
    const response = await fetch(url, {
      method,
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form)
    });

    if (response.ok) {
      onRefresh(); // Refresh data from backend
    }
  } catch (error) {
    console.error('Error:', error);
  }
};
```

#### Backend State Management
```python
# Backend state synchronization
def update_camera_in_db(camera: CameraConfig):
    """Update camera in database and sync state"""
    try:
        conn = get_db_connection()
        cursor = conn.cursor()
        
        cursor.execute("""
            INSERT INTO cameras (name, rtsp_url, location, enabled, apps, model_config)
            VALUES (%s, %s, %s, %s, %s, %s)
            ON CONFLICT (name) DO UPDATE SET
                rtsp_url = EXCLUDED.rtsp_url,
                location = EXCLUDED.location,
                enabled = EXCLUDED.enabled,
                apps = EXCLUDED.apps,
                model_config = EXCLUDED.model_config
        """, (
            camera.name, camera.rtsp_url, camera.location,
            camera.enabled, json.dumps(camera.apps),
            json.dumps(camera.model_config)
        ))
        
        conn.commit()
        conn.close()
        
        # Update in-memory state
        camera_store = [cam for cam in camera_store if cam.name != camera.name]
        camera_store.append(camera)
        
    except Exception as e:
        logger.error(f"Database error: {e}")
        raise
```

## 🤖 YOLO Model Integration

### 1. Model Configuration System

#### Centralized Model Management
```python
# Model configurations for each detection system
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

#### Model Validation System
```python
def validate_model_path(app_name: str) -> bool:
    """Validate that the model file exists for the app"""
    model_config = MODEL_CONFIGS.get(app_name)
    if not model_config:
        return False
    
    model_path = model_config['model_path']
    
    # Check if model file exists
    if os.path.exists(model_path):
        logger.info(f"Model found for {app_name}: {model_path}")
        return True
    else:
        logger.warning(f"Model not found for {app_name}: {model_path}")
        return False

def get_model_info(app_name: str) -> Dict[str, Any]:
    """Get detailed model information"""
    model_config = MODEL_CONFIGS.get(app_name, {})
    model_path = model_config.get('model_path', '')
    
    return {
        'model_path': model_path,
        'model_type': model_config.get('model_type', ''),
        'classes': model_config.get('classes', []),
        'confidence_threshold': model_config.get('confidence_threshold', 0.5),
        'frame_skip': model_config.get('frame_skip', 3),
        'exists': os.path.exists(model_path),
        'size_mb': get_model_size(model_path) if os.path.exists(model_path) else 0
    }
```

### 2. Model Loading and Inference

#### Cash Detection Model
```python
class MultiCameraCashDetector:
    def setup_model(self):
        """Setup YOLO model with error handling"""
        try:
            logger.info(f"Loading YOLO model from {MODEL_PATH}")
            self.model = YOLO(MODEL_PATH)
            logger.info("YOLO model loaded successfully")
        except Exception as e:
            logger.error(f"Failed to load YOLO model: {e}")
            raise
    
    def detect_cash(self, frame):
        """Detect cash in frame using YOLO model"""
        try:
            start_time = time.time()
            
            results = self.model(
                frame, 
                conf=CONFIDENCE_THRESHOLD, 
                iou=IOU_THRESHOLD, 
                verbose=False
            )
            
            detections = []
            for result in results:
                boxes = result.boxes
                if boxes is not None:
                    for box in boxes:
                        x1, y1, x2, y2 = box.xyxy[0].cpu().numpy()
                        confidence = box.conf[0].cpu().numpy()
                        class_id = int(box.cls[0].cpu().numpy())
                        
                        detections.append(([x1, y1, x2, y2], confidence, class_id))
            
            processing_time = time.time() - start_time
            return detections, processing_time
            
        except Exception as e:
            logger.error(f"Error in cash detection: {e}")
            return [], 0
```

#### Theft Detection Model (YOLO Pose)
```python
class TheftAppDetector:
    def setup_models(self):
        """Load YOLO pose model for theft detection"""
        try:
            # Load YOLO pose model
            self.pose_model = YOLO('models/best.pt')
            self.pose_model.to(self.device)
            
            # Load XGBoost classifier for pose analysis
            self.classifier = xgb.XGBClassifier()
            self.classifier.load_model('models/pose_classifier.json')
            
            logging.info("Models loaded successfully")
        except Exception as e:
            logging.error(f"Failed to load models: {e}")
            raise
    
    def process_pose_detection(self, frame):
        """Process pose detection for theft analysis"""
        try:
            # Run pose detection
            results = self.pose_model(frame, verbose=False)
            
            poses = []
            for result in results:
                if result.keypoints is not None:
                    keypoints = result.keypoints.data[0].cpu().numpy()
                    poses.append(keypoints)
            
            return poses
        except Exception as e:
            logging.error(f"Pose detection error: {e}")
            return []
```

#### Monitor Detection Model
```python
class MonitorDetector:
    def setup_models(self):
        """Load custom model for monitor detection"""
        try:
            # Load custom monitor detection model
            self.model = YOLO('monitor_model.pt')
            self.model.to(self.device)
            
            logging.info("Monitor detection model loaded successfully")
        except Exception as e:
            logging.error(f"Failed to load monitor model: {e}")
            raise
    
    def detect_monitor_status(self, frame):
        """Detect monitor on/off status"""
        try:
            results = self.model(frame, conf=0.7, verbose=False)
            
            detections = []
            for result in results:
                boxes = result.boxes
                if boxes is not None:
                    for box in boxes:
                        x1, y1, x2, y2 = box.xyxy[0].cpu().numpy()
                        confidence = box.conf[0].cpu().numpy()
                        class_id = int(box.cls[0].cpu().numpy())
                        class_name = ['monitor_off', 'monitor_on'][class_id]
                        
                        detections.append({
                            'bbox': [x1, y1, x2, y2],
                            'confidence': confidence,
                            'class': class_name
                        })
            
            return detections
        except Exception as e:
            logging.error(f"Monitor detection error: {e}")
            return []
```

### 3. Model Performance Optimization

#### Frame Skipping for Performance
```python
def process_camera(self, camera_id):
    """Process single camera with frame skipping"""
    while self.running and camera.running:
        try:
            frame = camera.get_frame()
            if frame is None:
                time.sleep(0.1)
                continue
            
            # Process every nth frame for performance
            if self.frame_counters[camera_id] % FRAME_SKIP == 0:
                # Detect objects
                detections, processing_time = self.detect_objects(frame)
                
                # Process detections
                self.process_detections(detections, camera_id)
            
            self.frame_counters[camera_id] += 1
            
        except Exception as e:
            logger.error(f"Error processing camera {camera_id}: {e}")
            time.sleep(1)
```

#### GPU Memory Management
```python
def setup_device(self):
    """Setup CUDA device with memory management"""
    if torch.cuda.is_available():
        device = torch.device('cuda:0')
        
        # Set memory fraction to prevent OOM
        torch.cuda.set_per_process_memory_fraction(0.8)
        
        # Clear cache
        torch.cuda.empty_cache()
        
        print(f"Using CUDA device: {torch.cuda.get_device_name(0)}")
        print(f"GPU memory: {torch.cuda.get_device_properties(0).total_memory / 1024**3:.1f} GB")
    else:
        device = torch.device('cpu')
        print("Using CPU device")
    
    return device
```

## 🔧 Configuration and Setup

### 1. Environment Setup

#### Backend Configuration
```bash
# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_NAME=sakshi_ai_db
DB_USER=sakshi_user
DB_PASSWORD=sakshi_password

# Telegram Configuration
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id

# GPU Configuration
NVIDIA_VISIBLE_DEVICES=all
CUDA_VISIBLE_DEVICES=0
```

#### Frontend Configuration
```javascript
// API Configuration
const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:8000';
const STREAM_BASE_URL = process.env.REACT_APP_STREAM_URL || 'http://localhost:8000';

// WebSocket Configuration
const WS_URL = process.env.REACT_APP_WS_URL || 'ws://localhost:8000/ws';
```

### 2. Model Deployment

#### Model File Structure
```
Sakshi.Ai/
├── Cash_Detection_Docker/
│   ├── 27-july-2025.pt          # Cash detection model
│   └── cash_detector.py
├── yolo-pose-shoplifting/
│   ├── models/
│   │   ├── best.pt              # Theft detection model
│   │   └── pose_classifier.json # Pose classifier
│   └── main_docker.py
├── monitor_detection_system/
│   ├── monitor_model.pt         # Monitor detection model
│   └── main_docker.py
└── backend/
    └── main.py
```

#### Model Validation Script
```python
def validate_all_models():
    """Validate all models before deployment"""
    validation_results = {}
    
    for app_name, config in MODEL_CONFIGS.items():
        model_path = config['model_path']
        
        # Check file exists
        exists = os.path.exists(model_path)
        
        # Check file size
        size_mb = 0
        if exists:
            size_mb = os.path.getsize(model_path) / (1024 * 1024)
        
        # Test model loading
        loadable = False
        if exists:
            try:
                model = YOLO(model_path)
                loadable = True
            except Exception as e:
                logger.error(f"Failed to load {app_name} model: {e}")
        
        validation_results[app_name] = {
            'exists': exists,
            'size_mb': size_mb,
            'loadable': loadable,
            'path': model_path
        }
    
    return validation_results
```

## 🚀 Quick Start

### 1. Start Backend
```bash
cd backend
python -m uvicorn main:app --reload --host 0.0.0.0 --port 5000
```

### 2. Start Frontend
```bash
cd frontend
npm start
```

### 3. Access Application
- **Frontend**: `http://localhost:3000`
- **Backend API**: `http://localhost:5000`
- **API Docs**: `http://localhost:5000/docs`

## 📊 Monitoring and Analytics

### 1. Performance Monitoring
```python
# Performance tracking
self.fps_counters = {}
self.processing_times = {}
self.frame_counters = {}

# Calculate FPS and processing time
if time.time() - self.fps_start_times[camera_id] >= 1.0:
    fps = self.fps_counters[camera_id] / (time.time() - self.fps_start_times[camera_id])
    avg_time = np.mean(self.processing_times[camera_id]) if self.processing_times[camera_id] else 0
    logger.debug(f"Camera {camera_id}: FPS={fps:.1f}, AvgTime={avg_time*1000:.1f}ms")
```

### 2. Database Analytics
```sql
-- Detection analytics
SELECT 
    detection_type,
    COUNT(*) as total_detections,
    AVG(confidence) as avg_confidence,
    DATE(created_at) as detection_date
FROM detections 
GROUP BY detection_type, DATE(created_at)
ORDER BY detection_date DESC;
```

## 🛠️ Development Workflow

### 1. Model Development
```bash
# Test model loading
python -c "
from ultralytics import YOLO
model = YOLO('Cash_Detection_Docker/27-july-2025.pt')
print('Model loaded successfully')
"

# Validate model performance
python test_model_performance.py
```

### 2. API Development
```bash
# Test API endpoints
curl http://localhost:5000/api/health
curl http://localhost:5000/api/models/available
curl http://localhost:5000/api/apps/status
```

### 3. Frontend Development
```bash
# Start development server with hot reload
npm start

# Build for production
npm run build
```

## 🔍 Troubleshooting

### 1. Model Loading Issues
```bash
# Check model file exists
ls -la Cash_Detection_Docker/27-july-2025.pt

# Validate model path
curl http://localhost:5000/api/models/available

# Test model loading manually
python -c "
from ultralytics import YOLO
try:
    model = YOLO('Cash_Detection_Docker/27-july-2025.pt')
    print('Model loaded successfully')
except Exception as e:
    print(f'Error: {e}')
"
```

### 2. Threading Issues
```bash
# Check thread status
ps aux | grep python

# Monitor thread performance
python -c "
import threading
print(f'Active threads: {threading.active_count()}')
for thread in threading.enumerate():
    print(f'Thread: {thread.name}, Daemon: {thread.daemon}')
"
```

### 3. Database Connection Issues
```bash
# Test database connection
psql -h localhost -U sakshi_user -d sakshi_ai_db -c "SELECT 1;"

# Check database logs
tail -f /var/log/postgresql/postgresql-*.log
```

## 📈 Performance Optimization

### 1. GPU Optimization
```python
# GPU memory management
torch.cuda.empty_cache()
torch.cuda.set_per_process_memory_fraction(0.8)

# Batch processing
def process_batch(frames):
    batch = torch.stack(frames)
    results = model(batch)
    return results
```

### 2. Threading Optimization
```python
# Thread pool for I/O operations
from concurrent.futures import ThreadPoolExecutor

with ThreadPoolExecutor(max_workers=4) as executor:
    futures = [executor.submit(process_camera, cam_id) for cam_id in camera_ids]
    results = [future.result() for future in futures]
```

### 3. Memory Optimization
```python
# Frame skipping for performance
FRAME_SKIP = 3  # Process every 3rd frame

# Memory cleanup
def cleanup_memory():
    import gc
    gc.collect()
    if torch.cuda.is_available():
        torch.cuda.empty_cache()
```

---

**Sakshi.Ai** - Empowering Security with AI 🚀

This comprehensive system integrates multiple YOLO models with advanced threading, real-time communication, and robust error handling for enterprise-grade surveillance applications. 
