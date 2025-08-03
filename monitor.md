Monitor Detection System
This project is a comprehensive monitoring system designed to detect the status of monitors (ON/OFF) using live video feeds from RTSP cameras. It leverages the YOLOv8 object detection model for real-time analysis, logs events to a PostgreSQL database, and sends instant alerts via Telegram.

✨ Features
Real-Time Detection: Uses the YOLOv8 model with OpenCV to process multiple camera streams concurrently.

Persistent Storage: Logs all detection events, daily statistics, and alert history in a PostgreSQL database for auditing and analysis.

Instant Notifications: Sends detailed alerts with screenshots to a specified Telegram chat when a monitor's status changes.

Automated Reporting: Generates and sends a daily summary report of all monitoring activities at midnight.

Scalable & Resilient: Uses multi-threading to handle camera processing, alerts, and reporting independently. Includes automatic reconnection logic for lost camera streams.

Highly Configurable: System behavior, database credentials, and camera streams are easily configured through a cameras.json file and environment variables.

GPU Accelerated: Built to utilize CUDA for high-performance, real-time deep learning inference.

🏗️ System Architecture
The following diagram illustrates the high-level architecture and data flow of the monitor detection system.

graph TD
    subgraph "Input Sources"
        direction LR
        CAM1[Camera 1<br>(RTSP Stream)]
        CAM2[Camera 2<br>(RTSP Stream)]
        CAMN[... ]
    end

    subgraph "MonitorDetector Application"
        direction TB
        MAIN[Main Application Thread]
        MAIN --> THREADS{Spawn Threads}
        
        subgraph "Real-Time Processing (per camera)"
            direction LR
            CT1[Camera 1 Processor<br>(YOLOv8 Detection)]
            CT2[Camera 2 Processor<br>(YOLOv8 Detection)]
            CTN[...]
        end

        subgraph "Background Workers"
            direction LR
            AW[Alert Worker]
            RS[Report Scheduler]
        end

        THREADS --> CT1 & CT2 & CTN
        THREADS --> AW & RS

        CT1 & CT2 & CTN --> DB[(PostgreSQL DB)]
        CT1 & CT2 & CTN --> EQ[Event Queue]

        EQ --> AW
    end

    subgraph "External Services"
        direction TB
        DB[(PostgreSQL DB)]
        TG[Telegram API]
    end

    AW --> TG
    RS --> DB
    RS --> TG

    CAM1 --> CT1
    CAM2 --> CT2
    CAMN --> CTN

    style DB fill:#cce5ff,stroke:#b8daff
    style TG fill:#d4edda,stroke:#c3e6cb

📋 Prerequisites
Before you begin, ensure you have the following installed and configured:

Python 3.8+

A running PostgreSQL Server

An NVIDIA GPU with a compatible CUDA version installed

A Telegram Bot Token and a Chat ID

⚙️ Setup and Installation
Follow these steps to get the system up and running.

1. Clone the Repository

git clone <your-repo-url>
cd <your-repo-name>

2. Install Dependencies
Create a requirements.txt file with the following content:

torch
torchvision
torchaudio
ultralytics
opencv-python
psycopg2-binary
requests
python-dotenv

Then, install the packages:

pip install -r requirements.txt

3. Configure Environment Variables
Create a .env file in the root directory of the project to store your sensitive credentials. The application will load these automatically.

# PostgreSQL Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_NAME=monitor_db
DB_USER=postgres
DB_PASSWORD=your_secure_password

# Telegram Bot Configuration
TELEGRAM_BOT_TOKEN=your_telegram_bot_token
TELEGRAM_CHAT_ID=your_telegram_chat_id

4. Configure Cameras
The application looks for a cameras.json file to define the camera streams to monitor. If the file does not exist on the first run, a default template will be created for you. You can then edit it with your actual RTSP URLs and camera details.

Example cameras.json:

{
  "cameras": [
    {
      "id": "camera_1",
      "name": "Main Office Cam",
      "location": "Office Area 1",
      "rtsp_url": "rtsp://user:pass@192.168.1.101:554/stream1",
      "enabled": true
    },
    {
      "id": "camera_2",
      "name": "Server Room Cam",
      "location": "Data Center",
      "rtsp_url": "rtsp://user:pass@192.168.1.102:554/stream1",
      "enabled": true
    }
  ],
  "settings": {
    "frame_skip": 3,
    "confidence_threshold": 0.55,
    "detection_timeout": 5,
    "max_cameras": 10
  }
}

5. YOLOv8 Model
The script is configured to use the yolov8n.pt model. The ultralytics library will automatically download this model on the first run if it's not found locally.

▶️ Running the Application
Once the setup is complete, you can start the monitoring system with a single command:

python your_script_name.py

The application will initialize all components, connect to the database, start processing camera feeds, and begin monitoring for events. You can stop the system by pressing CTRL+C in the terminal.

🗄️ Database Schema
The application automatically creates the following tables in the specified PostgreSQL database:

cameras: Stores the configuration for each camera.

monitor_events: Contains a detailed log of every "monitor on" and "monitor off" event detected.

daily_counts: Aggregates the number of on/off events per camera for each day.

telegram_alerts: Keeps a record of all notifications sent via Telegram.
