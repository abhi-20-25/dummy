# Monitor Detection System

This project is a comprehensive monitoring system designed to detect the status of monitors (ON/OFF) using live video feeds from RTSP cameras. It leverages the YOLOv8 object detection model for real-time analysis, logs events to a PostgreSQL database, and sends instant alerts via Telegram.

## ✨ Features

- **Real-Time Detection**: Uses the YOLOv8 model with OpenCV to process multiple camera streams concurrently.
- **Persistent Storage**: Logs all detection events, daily statistics, and alert history in a PostgreSQL database for auditing and analysis.
- **Instant Notifications**: Sends detailed alerts with screenshots to a specified Telegram chat when a monitor's status changes.
- **Automated Reporting**: Generates and sends a daily summary report of all monitoring activities at midnight.
- **Scalable & Resilient**: Uses multi-threading to handle camera processing, alerts, and reporting independently. Includes automatic reconnection logic for lost camera streams.
- **Highly Configurable**: System behavior, database credentials, and camera streams are easily configured through a `cameras.json` file and environment variables.
- **GPU Accelerated**: Built to utilize CUDA for high-performance, real-time deep learning inference.

---

## 🏗️ System Architecture

The following diagram illustrates the high-level architecture and data flow of the monitor detection system.

```mermaid
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
