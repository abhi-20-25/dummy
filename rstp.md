# 📹 RTSP Camera Configuration Guide

This guide shows how to add RTSP camera links to all Sakshi repository projects.

## 🔗 RTSP URL Formats

### Common RTSP URL Patterns
```
# Hikvision cameras
rtsp://username:password@192.168.1.100:554/Streaming/Channels/101
rtsp://username:password@192.168.1.100:554/h264/ch1/main/av_stream

# Dahua cameras
rtsp://username:password@192.168.1.100:554/cam/realmonitor?channel=1&subtype=0

# Axis cameras
rtsp://username:password@192.168.1.100:554/axis-media/media.amp

# Generic IP cameras
rtsp://username:password@192.168.1.100:554/stream1
rtsp://username:password@192.168.1.100:554/live

# Local network cameras
rtsp://192.168.1.100:554/stream
rtsp://192.168.1.100:8080/video

# Public IP cameras (if accessible)
rtsp://public_ip:554/stream
```

## 🎯 Project-Specific Configurations

### 1. Cash Detection System (`Cash_Detection_Docker`)

**File**: `Cash_Detection_Docker/cameras.json`

```json
{
  "cameras": [
    {
      "name": "Store Entrance",
      "rtsp_url": "rtsp://username:password@192.168.1.100:554/Streaming/Channels/101",
      "location": "Main Entrance",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.5,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    },
    {
      "name": "Cash Register 1",
      "rtsp_url": "rtsp://username:password@192.168.1.101:554/Streaming/Channels/101",
      "location": "Cash Register Area",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.6,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    },
    {
      "name": "Cash Register 2",
      "rtsp_url": "rtsp://username:password@192.168.1.102:554/Streaming/Channels/101",
      "location": "Secondary Register",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.5,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    },
    {
      "name": "Back Office",
      "rtsp_url": "rtsp://username:password@192.168.1.103:554/Streaming/Channels/101",
      "location": "Office Area",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.7,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    }
  ],
  "settings": {
    "model_path": "27-july-2025.pt",
    "frame_skip": 2,
    "frame_width": 640,
    "frame_height": 480,
    "fps": 30,
    "enable_display": true,
    "enable_telegram": true,
    "screenshot_dir": "screenshots",
    "screenshot_quality": 95,
    "max_disappeared": 30,
    "min_hits": 3,
    "log_level": "INFO"
  }
}
```

### 2. People Count System (`docker_people_count`)

**File**: `docker_people_count/config.env`

```bash
# RTSP URLs (comma-separated)
RTSP_URLS=rtsp://username:password@192.168.1.100:554/Streaming/Channels/101,rtsp://username:password@192.168.1.101:554/Streaming/Channels/101,rtsp://username:password@192.168.1.102:554/Streaming/Channels/101

# Alternative: Video files (comma-separated)
VIDEO_FILES=videos/test1.mp4,videos/test2.mp4

# Database settings
DB_HOST=localhost
DB_PORT=5432
DB_NAME=people_count
DB_USER=postgres
DB_PASSWORD=your_password

# Telegram settings
TELEGRAM_BOT_TOKEN=your_bot_token
TELEGRAM_CHAT_ID=your_chat_id

# Detection settings
CONFIDENCE_THRESHOLD=0.5
IOU_THRESHOLD=0.45
FRAME_SKIP=2
```

### 3. Monitor Detection System (`monitor_detection_system`)

**File**: `monitor_detection_system/cameras.json`

```json
{
  "cameras": [
    {
      "name": "Office Monitor 1",
      "rtsp_url": "rtsp://username:password@192.168.1.100:554/Streaming/Channels/101",
      "location": "Main Office",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.7,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    },
    {
      "name": "Office Monitor 2",
      "rtsp_url": "rtsp://username:password@192.168.1.101:554/Streaming/Channels/101",
      "location": "Secondary Office",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.7,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    },
    {
      "name": "Security Monitor",
      "rtsp_url": "rtsp://username:password@192.168.1.102:554/Streaming/Channels/101",
      "location": "Security Room",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.8,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    }
  ],
  "settings": {
    "frame_skip": 5,
    "confidence_threshold": 0.7,
    "recording_duration": 10,
    "max_cameras": 10,
    "model_path": "monitor_detection_system/best.pt",
    "model_type": "custom",
    "classes": [
      "monitor_on",
      "monitor_off"
    ]
  }
}
```

### 4. Shoplifting Detection System (`yolo-pose-shoplifting`)

**File**: `yolo-pose-shoplifting/cameras.json`

```json
{
  "cameras": [
    {
      "name": "Store Aisle 1",
      "rtsp_url": "rtsp://username:password@192.168.1.100:554/Streaming/Channels/101",
      "location": "Main Aisle",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.6,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    },
    {
      "name": "Store Aisle 2",
      "rtsp_url": "rtsp://username:password@192.168.1.101:554/Streaming/Channels/101",
      "location": "Electronics Section",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.6,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    },
    {
      "name": "Store Aisle 3",
      "rtsp_url": "rtsp://username:password@192.168.1.102:554/Streaming/Channels/101",
      "location": "Clothing Section",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.6,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    },
    {
      "name": "Store Exit",
      "rtsp_url": "rtsp://username:password@192.168.1.103:554/Streaming/Channels/101",
      "location": "Exit Area",
      "enabled": true,
      "model_config": {
        "confidence_threshold": 0.7,
        "iou_threshold": 0.45,
        "detection_timeout": 2.0
      }
    }
  ],
  "settings": {
    "frame_skip": 2,
    "confidence_threshold": 0.6,
    "recording_duration": 10,
    "max_cameras": 10,
    "model_path": "yolo-pose-shoplifting/models/yolo11n-pose.pt",
    "model_type": "yolo_pose",
    "classes": [
      "person",
      "shoplifting",
      "suspicious"
    ]
  }
}
```

## 🔧 Configuration Scripts

### Quick RTSP Setup Script

Create a script to easily add RTSP cameras:

```bash
#!/bin/bash
# add_rtsp_cameras.sh

echo "📹 Adding RTSP cameras to all projects..."

# Function to add RTSP camera to JSON file
add_rtsp_to_json() {
    local file=$1
    local camera_name=$2
    local rtsp_url=$3
    local location=$4
    
    # Create backup
    cp "$file" "${file}.backup"
    
    # Add camera to JSON
    jq --arg name "$camera_name" \
       --arg url "$rtsp_url" \
       --arg loc "$location" \
       '.cameras += [{"name": $name, "rtsp_url": $url, "location": $loc, "enabled": true, "model_config": {"confidence_threshold": 0.5, "iou_threshold": 0.45, "detection_timeout": 2.0}}]' \
       "$file" > "${file}.tmp" && mv "${file}.tmp" "$file"
    
    echo "✅ Added camera '$camera_name' to $file"
}

# Add cameras to each project
add_rtsp_to_json "Cash_Detection_Docker/cameras.json" "Store Camera 1" "rtsp://username:password@192.168.1.100:554/Streaming/Channels/101" "Main Store"
add_rtsp_to_json "monitor_detection_system/cameras.json" "Office Camera 1" "rtsp://username:password@192.168.1.101:554/Streaming/Channels/101" "Main Office"
add_rtsp_to_json "yolo-pose-shoplifting/cameras.json" "Aisle Camera 1" "rtsp://username:password@192.168.1.102:554/Streaming/Channels/101" "Main Aisle"

echo "🎉 RTSP cameras added successfully!"
```

## 🛠️ Testing RTSP Connections

### Test RTSP URL Script

```bash
#!/bin/bash
# test_rtsp.sh

echo "🔍 Testing RTSP connections..."

# Function to test RTSP URL
test_rtsp() {
    local url=$1
    local name=$2
    
    echo "Testing: $name"
    echo "URL: $url"
    
    # Test with ffmpeg
    timeout 10 ffmpeg -i "$url" -t 5 -f null - 2>/dev/null
    if [ $? -eq 0 ]; then
        echo "✅ Connection successful"
    else
        echo "❌ Connection failed"
    fi
    echo ""
}

# Test your RTSP URLs
test_rtsp "rtsp://username:password@192.168.1.100:554/Streaming/Channels/101" "Camera 1"
test_rtsp "rtsp://username:password@192.168.1.101:554/Streaming/Channels/101" "Camera 2"
test_rtsp "rtsp://username:password@192.168.1.102:554/Streaming/Channels/101" "Camera 3"
```

## 📋 Common RTSP Camera Brands

### Hikvision
```
rtsp://username:password@192.168.1.100:554/Streaming/Channels/101
rtsp://username:password@192.168.1.100:554/h264/ch1/main/av_stream
rtsp://username:password@192.168.1.100:554/h264/ch1/sub/av_stream
```

### Dahua
```
rtsp://username:password@192.168.1.100:554/cam/realmonitor?channel=1&subtype=0
rtsp://username:password@192.168.1.100:554/cam/realmonitor?channel=1&subtype=1
```

### Axis
```
rtsp://username:password@192.168.1.100:554/axis-media/media.amp
rtsp://username:password@192.168.1.100:554/axis-media/media.amp?videocodec=h264
```

### Generic IP Cameras
```
rtsp://192.168.1.100:554/stream
rtsp://192.168.1.100:8080/video
rtsp://192.168.1.100:8554/stream
```

## 🔐 Security Considerations

### 1. Use Strong Passwords
```bash
# Change default passwords
# Username: admin
# Password: Use strong password (12+ characters)
```

### 2. Network Security
```bash
# Use VLAN for cameras
# Enable firewall rules
# Use HTTPS for web interface
```

### 3. RTSP Authentication
```bash
# Enable authentication
# Use unique credentials per camera
# Regularly rotate passwords
```

## 🚀 Quick Setup Commands

### 1. Add RTSP Camera to All Projects
```bash
# Create the setup script
cat > add_rtsp_cameras.sh << 'EOF'
#!/bin/bash
echo "📹 Adding RTSP cameras..."

# Add to Cash Detection
jq '.cameras += [{"name": "Store Camera", "rtsp_url": "rtsp://username:password@192.168.1.100:554/Streaming/Channels/101", "location": "Store", "enabled": true, "model_config": {"confidence_threshold": 0.5, "iou_threshold": 0.45, "detection_timeout": 2.0}}]' Cash_Detection_Docker/cameras.json > temp.json && mv temp.json Cash_Detection_Docker/cameras.json

# Add to Monitor Detection
jq '.cameras += [{"name": "Office Camera", "rtsp_url": "rtsp://username:password@192.168.1.101:554/Streaming/Channels/101", "location": "Office", "enabled": true, "model_config": {"confidence_threshold": 0.7, "iou_threshold": 0.45, "detection_timeout": 2.0}}]' monitor_detection_system/cameras.json > temp.json && mv temp.json monitor_detection_system/cameras.json

# Add to Shoplifting Detection
jq '.cameras += [{"name": "Aisle Camera", "rtsp_url": "rtsp://username:password@192.168.1.102:554/Streaming/Channels/101", "location": "Aisle", "enabled": true, "model_config": {"confidence_threshold": 0.6, "iou_threshold": 0.45, "detection_timeout": 2.0}}]' yolo-pose-shoplifting/cameras.json > temp.json && mv temp.json yolo-pose-shoplifting/cameras.json

echo "✅ RTSP cameras added!"
EOF

chmod +x add_rtsp_cameras.sh
./add_rtsp_cameras.sh
```

### 2. Test RTSP Connections
```bash
# Install ffmpeg if not installed
sudo apt install ffmpeg

# Test RTSP URLs
ffmpeg -i "rtsp://username:password@192.168.1.100:554/Streaming/Channels/101" -t 5 -f null - 2>/dev/null && echo "✅ Camera 1 working" || echo "❌ Camera 1 failed"
```

### 3. Update Environment Variables
```bash
# For docker_people_count
echo "RTSP_URLS=rtsp://username:password@192.168.1.100:554/Streaming/Channels/101,rtsp://username:password@192.168.1.101:554/Streaming/Channels/101" >> docker_people_count/config.env
```

## 📞 Troubleshooting

### Common Issues

1. **Connection Refused**
   - Check IP address and port
   - Verify camera is online
   - Check network connectivity

2. **Authentication Failed**
   - Verify username/password
   - Check camera credentials
   - Try different authentication method

3. **Stream Not Found**
   - Check RTSP URL format
   - Verify camera model
   - Try different stream path

4. **Poor Performance**
   - Reduce frame rate
   - Lower resolution
   - Use sub-stream instead of main stream

### Debug Commands
```bash
# Test network connectivity
ping 192.168.1.100

# Test port connectivity
telnet 192.168.1.100 554

# Test RTSP with curl
curl -I rtsp://username:password@192.168.1.100:554/Streaming/Channels/101

# Test with VLC
vlc rtsp://username:password@192.168.1.100:554/Streaming/Channels/101
```

## 🎯 Summary

To add RTSP links to your projects:

1. **Update camera configuration files** with your RTSP URLs
2. **Test connections** using ffmpeg or VLC
3. **Update environment variables** for docker-based projects
4. **Restart services** to apply changes
5. **Monitor logs** for connection issues

Remember to replace `username:password@192.168.1.100` with your actual camera credentials and IP addresses! 
