# 💰 Cash Detection System - Containerized

A powerful, containerized cash detection system that monitors multiple cameras (including RTSP streams), detects cash using a trained YOLO model, and sends Telegram alerts when cash is detected for more than 2 seconds. All data is stored in a PostgreSQL database.

## 🚀 Features

- **Multi-Camera Support**: Handle multiple cameras simultaneously (RTSP, webcam, video files).
- **Real-time Detection**: Uses your trained YOLO model for cash detection.
- **Database Storage**: PostgreSQL database for storing detections, alerts, and system logs.
- **Telegram Integration**: Send alerts with screenshots when cash is detected for >2 seconds.
- **Docker Containerized**: Single container with everything included.
- **Environment Configuration**: Easy configuration via environment variables.
- **Unique ID Tracking**: Each cash detection gets a unique identifier.
- **Performance Monitoring**: Real-time FPS and processing time tracking.
- **Camera Management**: Easy addition and management of multiple cameras.

## 📋 Requirements

- Docker and Docker Compose
- At least 4GB RAM
- GPU support (optional, for better performance)

## 🏗️ Architecture

### System Overview

```mermaid
graph TD
    subgraph Initialization Phase
        direction LR
        A[Start] --> B(new MultiCameraCashDetector);
        B --> C(setup_model);
        C --> D(setup_components);
        D --> E(setup_cameras);
    end

    E --> F[Main Controller];

    subgraph "Concurrent Processing"
        direction TB
        
        subgraph "Input Feeds"
            direction LR
            CAM1[fa:fa-video Camera 1];
            CAM2[fa:fa-video Camera 2];
            CAM3[fa:fa-video Camera 3];
        end
        
        F --> T1[Thread 1];
        F --> T2[Thread 2];
        F --> T3[Thread 3];

        CAM1 --> T1;
        CAM2 --> T2;
        CAM3 --> T3;

        subgraph "Shared Resource"
            SHARED_MODEL[fa:fa-cogs Single YOLO Instance];
        end

        T1 -- Frame --> SHARED_MODEL;
        T2 -- Frame --> SHARED_MODEL;
        T3 -- Frame --> SHARED_MODEL;

        SHARED_MODEL -- Detections --> R1[Output 1];
        SHARED_MODEL -- Detections --> R2[Output 2];
        SHARED_MODEL -- Detections --> R3[Output 3];

    end

    R1 --> FINAL_OUT[Display & Logging];
    R2 --> FINAL_OUT;
    R3 --> FINAL_OUT;

    FINAL_OUT --> Z[End];
