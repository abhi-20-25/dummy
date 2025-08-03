# 📚 Sakshi.Ai User Manual

## 🚀 Quick Start Guide

### Prerequisites
- Ubuntu 20.04+ with NVIDIA RTX 5 series GPU
- 8GB+ RAM
- 50GB+ storage
- Internet connection

### 1. Automated Deployment (Recommended)

```bash
# Download the deployment script
wget https://raw.githubusercontent.com/your-repo/sakshi-ai/main/deploy_sakshi_ai.sh

# Make it executable
chmod +x deploy_sakshi_ai.sh

# Run the deployment
./deploy_sakshi_ai.sh
```

### 2. Manual Setup

```bash
# Clone the repository
git clone https://github.com/your-repo/sakshi-ai.git
cd sakshi-ai

# Run deployment script
./deploy_sakshi_ai.sh
```

### 3. Configuration

```bash
# Update Telegram credentials
nano /opt/sakshi-ai/.env

# Start the system
sudo systemctl start sakshi-ai

# Check status
sudo systemctl status sakshi-ai
```

## 🌐 Web Interface

### Access Points
- **Frontend Dashboard**: `http://your-server-ip:3000`
- **Backend API**: `http://your-server-ip:5000`
- **API Documentation**: `http://your-server-ip:5000/docs`

### Dashboard Features

#### 1. Camera Management
- **Add Camera**: Click "Add Camera" button
  - Enter camera name (unique identifier)
  - Enter RTSP URL (camera stream URL)
  - Select detection apps (Cash, Theft, Monitor)
  - Click "Add" to save

- **Edit Camera**: Click edit icon on camera card
  - Modify camera settings
  - Change detection apps
  - Update RTSP URL

- **Delete Camera**: Click delete icon to remove camera

#### 2. Live Streams
- **Select Layout**: Choose from 1, 2, 4, 6, or 8 view layouts
- **Choose Cameras**: Select cameras for each view position
- **Start Streams**: Click play button on each camera
- **Fullscreen**: Click fullscreen icon for individual views

#### 3. App Status
- **View Status**: Real-time status of all detection applications
- **Start/Stop Apps**: Control individual applications
- **Monitor Cameras**: View which cameras are connected to each app
- **View Details**: Click settings icon for detailed information

#### 4. Analytics Dashboard
- **Key Metrics**: Total detections, alerts, cameras, running apps
- **Trends**: Detection patterns over time
- **Distribution**: Detection distribution across applications
- **Hourly Activity**: Activity patterns throughout the day

## 🔧 System Management

### Service Commands

```bash
# Start Sakshi.Ai
sudo systemctl start sakshi-ai

# Stop Sakshi.Ai
sudo systemctl stop sakshi-ai

# Check status
sudo systemctl status sakshi-ai

# Enable auto-start
sudo systemctl enable sakshi-ai

# Disable auto-start
sudo systemctl disable sakshi-ai
```

### Health Monitoring

```bash
# Run health check
/opt/sakshi-ai/health_check.sh

# View logs
docker-compose logs -f

# Check GPU status
nvidia-smi

# Check database
sudo -u postgres psql -c "\l"
```

### Container Management

```bash
# View all containers
docker ps -a

# View container logs
docker logs container_name

# Restart specific container
docker restart container_name

# Stop all containers
docker-compose down
```

## 📊 API Usage

### Camera Management

```bash
# Get all cameras
curl -X GET http://localhost:5000/api/cameras

# Add camera
curl -X POST http://localhost:5000/api/cameras \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Main Entrance",
    "rtsp": "rtsp://username:password@ip:port/stream",
    "apps": ["cash_detector", "theft_detector"]
  }'

# Update cameras
curl -X PUT http://localhost:5000/api/cameras \
  -H "Content-Type: application/json" \
  -d '{
    "cameras": [
      {
        "name": "Camera 1",
        "rtsp": "rtsp://url",
        "apps": ["cash_detector"]
      }
    ]
  }'

# Delete camera
curl -X DELETE http://localhost:5000/api/cameras/Camera%20Name
```

### Detection Systems

```bash
# Start camera processing
curl -X POST http://localhost:5000/api/start_camera/Camera%20Name

# Stop camera processing
curl -X POST http://localhost:5000/api/stop_camera/Camera%20Name

# Get analytics
curl -X GET http://localhost:5000/api/analytics
```

## 🔍 Troubleshooting

### Common Issues

#### 1. GPU Not Detected
```bash
# Check NVIDIA GPU
nvidia-smi

# Test Docker GPU support
docker run --rm --gpus all nvidia/cuda:11.8-base-ubuntu20.04 nvidia-smi

# Install NVIDIA drivers if needed
sudo apt-get install -y nvidia-driver-535
```

#### 2. Database Connection Failed
```bash
# Check PostgreSQL status
sudo systemctl status postgresql

# Check database
sudo -u postgres psql -c "\l"

# Restart PostgreSQL
sudo systemctl restart postgresql
```

#### 3. Telegram Notifications Not Working
```bash
# Check credentials
cat /opt/sakshi-ai/.env

# Test Telegram bot
curl -X POST https://api.telegram.org/botYOUR_BOT_TOKEN/getMe
```

#### 4. Camera Stream Issues
```bash
# Test RTSP stream
ffplay rtsp://your-camera-url

# Check network connectivity
ping camera-ip-address

# Test with VLC
vlc rtsp://your-camera-url
```

#### 5. Docker Issues
```bash
# Check Docker status
sudo systemctl status docker

# Restart Docker
sudo systemctl restart docker

# Check Docker Compose
docker-compose --version
```

### Log Locations

```bash
# Application logs
docker-compose logs -f

# System logs
sudo journalctl -u sakshi-ai -f

# GPU logs
nvidia-smi -l 1

# Database logs
sudo tail -f /var/log/postgresql/postgresql-*.log
```

### Performance Issues

#### 1. High CPU Usage
```bash
# Check system resources
htop

# Check GPU utilization
nvidia-smi -l 1

# Optimize frame skip settings
# Edit cameras.json and increase frame_skip value
```

#### 2. Memory Issues
```bash
# Check memory usage
free -h

# Check Docker memory
docker stats

# Restart containers
docker-compose restart
```

#### 3. Network Issues
```bash
# Check network connectivity
ping google.com

# Check port availability
netstat -tulpn | grep :3000
netstat -tulpn | grep :5000

# Check firewall
sudo ufw status
```

## 🔧 Configuration

### Environment Variables

Edit `/opt/sakshi-ai/.env`:

```bash
# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_NAME=sakshi_ai_db
DB_USER=sakshi_user
DB_PASSWORD=sakshi_password

# Telegram Configuration
TELEGRAM_BOT_TOKEN=your_telegram_bot_token_here
TELEGRAM_CHAT_ID=your_telegram_chat_id_here

# GPU Configuration
NVIDIA_VISIBLE_DEVICES=all
CUDA_VISIBLE_DEVICES=0

# Application Configuration
CONFIDENCE_THRESHOLD=0.55
DETECTION_TIMEOUT=10
FRAME_SKIP=3
```

### Camera Configuration

Edit camera configuration files:

**Cash Detection**: `/opt/sakshi-ai/Cash_Detection_Docker/cameras.json`
**Theft Detection**: `/opt/sakshi-ai/yolo-pose-shoplifting/cameras.json`
**Monitor Detection**: `/opt/sakshi-ai/monitor_detection_system/cameras.json`

Example:
```json
{
  "cameras": [
    {
      "id": "camera_1",
      "name": "Main Entrance",
      "location": "Front Door",
      "rtsp_url": "rtsp://username:password@192.168.1.100:554/stream1",
      "enabled": true
    }
  ],
  "settings": {
    "frame_skip": 3,
    "confidence_threshold": 0.55,
    "detection_timeout": 10
  }
}
```

## 📈 Monitoring

### Real-time Monitoring

```bash
# Monitor system resources
htop

# Monitor GPU usage
watch -n 1 nvidia-smi

# Monitor Docker containers
watch -n 1 docker ps

# Monitor logs
tail -f /opt/sakshi-ai/logs/*.log
```

### Analytics

Access analytics through:
1. **Web Dashboard**: `http://your-server-ip:3000`
2. **API Endpoint**: `http://your-server-ip:5000/api/analytics`
3. **Database Queries**: Direct PostgreSQL queries

### Alerts

- **Telegram Notifications**: Instant alerts with screenshots
- **Daily Reports**: Automated summary reports
- **System Alerts**: Service status notifications

## 🔒 Security

### Network Security
- **Firewall**: Automatic port management (22, 80, 443, 3000, 5000, 8000)
- **HTTPS**: SSL/TLS encryption for web interface
- **Access Control**: User authentication system

### Data Security
- **Encrypted Storage**: Database encryption
- **Secure Communication**: API authentication
- **Audit Logging**: Complete activity tracking

### Best Practices
1. **Change Default Passwords**: Update database and application passwords
2. **Use Secure RTSP URLs**: Implement authentication for camera streams
3. **Regular Updates**: Keep system and dependencies updated
4. **Backup Data**: Regular database and configuration backups
5. **Monitor Access**: Review access logs regularly

## 📞 Support

### Documentation
- **User Manual**: `/opt/sakshi-ai/USER_MANUAL.md`
- **API Documentation**: `http://your-server-ip:5000/docs`
- **Health Check**: `/opt/sakshi-ai/health_check.sh`

### Contact
- **GitHub Issues**: [Report Issues](https://github.com/your-repo/sakshi-ai/issues)
- **Documentation**: [Full Documentation](https://github.com/your-repo/sakshi-ai/wiki)

### Emergency Procedures

#### System Recovery
```bash
# Stop all services
sudo systemctl stop sakshi-ai

# Backup data
sudo pg_dump sakshi_ai_db > backup.sql

# Restart services
sudo systemctl start sakshi-ai

# Verify functionality
/opt/sakshi-ai/health_check.sh
```

#### Data Recovery
```bash
# Restore database
sudo -u postgres psql sakshi_ai_db < backup.sql

# Restart applications
docker-compose restart
```

---

**Sakshi.Ai** - Empowering Security with AI 🚀

For additional support, please refer to the main README.md file or contact the development team. 
