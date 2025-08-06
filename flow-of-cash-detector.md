# Multi-Camera Cash Detection System

![Python](https://img.shields.io/badge/python-3.8+-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

A real-time, multi-camera system for detecting and tracking cash using the YOLO object detection model. The system is designed for high performance and scalability, using multithreading to process multiple camera feeds concurrently. It includes features for performance monitoring, simple object tracking, and alert notifications via Telegram.



---

## Features

-   **Multi-Camera Support**: Concurrently process video streams from multiple cameras.
-   **Real-time Detection**: Utilizes a **YOLO model** for fast and accurate cash detection.
-   **Object Tracking**: A simple centroid-based tracker to follow detected objects across frames and assign unique IDs.
-   **Performance Optimized**:
    -   **Multithreading**: Each camera feed is processed in a separate thread for parallel execution.
    -   **Frame Skipping**: Configurable frame skipping to balance performance and accuracy.
    -   **Performance Metrics**: Logs FPS and average processing time for each camera.
-   **Alerting System**: Sends notifications with images via **Telegram** when cash is detected for a configurable duration.
-   **Configurable & Robust**:
    -   Easily configure model path, confidence thresholds, and other parameters.
    -   Extensive error handling and logging for reliable operation.
-   **Command-Line Interface**: Start the system and set the logging level easily from the terminal.

---

## How It Works

The system operates through the main `MultiCameraCashDetector` class, which orchestrates the entire detection pipeline:

1.  **Initialization**: The system first loads a pre-trained YOLO model, initializes a database logger, the optional `TelegramNotifier`, and the `CameraManager` which handles capturing frames from all configured camera sources.

2.  **Detection & Tracking**: For each camera, a dedicated thread is started. This thread continuously grabs frames, passes them to the YOLO model for detection, and uses a simple tracking algorithm to associate new detections with existing object tracks, assigning a persistent ID to each object.

3.  **Alert Logic**: A `CashTracker` instance for each camera monitors the tracked objects. If an object remains visible for a predefined period (e.g., `DETECTION_TIMEOUT`), it triggers an alert. The alert is logged to the database and, if configured, a notification with a snapshot is sent to a specified Telegram chat.

4.  **Shutdown**: The system can be stopped gracefully with `Ctrl+C`. The `stop_detection()` method ensures all camera threads are terminated, and resources are cleaned up properly.

---

## System Flowchart

This diagram outlines the two primary operational flows: the **Main Thread** for setup and control, and the parallel **Per-Camera Threads** for detection and tracking.

```mermaid
graph TD
    %% Define Styles
    classDef main fill:#e3f2fd,stroke:#333,stroke-width:2px;
    classDef camera fill:#e8f5e9,stroke:#333,stroke-width:2px;
    classDef decision fill:#fffde7,stroke:#f57f17,stroke-width:2px;
    classDef process fill:#f3e5f5,stroke:#6a1b9a,stroke-width:2px;
    classDef io fill:#e0f7fa,stroke:#006064,stroke-width:2px;
    classDef startend fill:#ffebee,stroke:#b71c1c,stroke-width:2px;

    %% Main Thread Initialization
    subgraph Main Thread Workflow
        A[Start]:::startend --> B(Parse CLI Arguments);
        B --> C(Create MultiCameraCashDetector Instance);
        C --> D(<b>setup_cameras()</b><br/>Initialize CameraManager);
        D --> E(<b>setup_components()</b><br/>Initialize Database & Telegram Notifier);
        E --> F(<b>setup_model()</b><br/>Load YOLO Model from path);
        F --> G(Call <b>run()</b> method);
        G --> H(<b>start_detection()</b><br/>Start a new thread for each camera)
        H -- Spawns --> I(Per-Camera Processing Thread);
        H --> J{Running? (Main Loop)};
        J -- Yes --> K(Sleep & periodically log system status);
        K --> J;
        J -- No (Ctrl+C Received) --> L(Call <b>stop_detection()</b>);
    end
    class A,L,M,N,O,P,Q startend;
    class B,C,D,E,F,G,H,J,K main;

    %% Per-Camera Processing Thread
    subgraph "Per-Camera Processing Thread (Runs in Parallel)"
        I --> T1[/Get Frame from Camera/];
        T1 --> T2{Frame Available?}:::decision;
        T2 -- No --> T1_wait(Wait briefly);
        T1_wait --> T1;
        T2 -- Yes --> T3{Process Frame?<br/>(Check frame_skip counter)}:::decision;
        T3 -- No (Skip) --> T_inc(Increment Frame Counter);
        T_inc --> T1;
        T3 -- Yes (Process) --> T4(<b>detect_cash()</b><br/>Run YOLO inference on frame):::process;
        T4 --> T5(<b>simple_track()</b><br/>Match detections to existing tracks):::process;
        T5 --> T6(Update CashTracker with new/updated detections);
        T6 --> T7(Remove tracks that have disappeared);
        T7 --> T8{Alert Condition Met?<br/>(e.g., object visible > 2s)}:::decision;
        T8 -- Yes --> T9(<b>Send Telegram Alert</b><br/>Log event to database):::io;
        T9 --> T10;
        T8 -- No --> T10(<b>draw_detections()</b><br/>Visualize boxes on frame);
        T10 --> T11(Update FPS & processing time counters);
        T11 --> T1;
    end
    class I,T1,T_inc,T1_wait,T4,T5,T6,T7,T9,T10,T11 camera;
    class T2,T3,T8 decision;
    class T1,T9 io;


    %% Shutdown Sequence
    subgraph "Shutdown Sequence"
        L --> M(Set main 'running' flag to False);
        M --> N(Stop all camera capture threads);
        N --> O(Wait for all processing threads to finish);
        O --> P(Cleanup camera and tracker resources);
        P --> Q[End]:::startend;
    end
