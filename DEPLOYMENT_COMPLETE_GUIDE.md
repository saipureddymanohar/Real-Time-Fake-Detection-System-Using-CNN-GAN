# 🚀 Complete Docker Deployment Guide

## Real-Time Fake Detection System - Full Deployment Instructions

This guide covers everything from running locally to deploying on cloud platforms.

---

## 📋 Table of Contents

1. [Local Development Setup](#local-development-setup)
2. [Production Deployment](#production-deployment)
3. [AWS EC2 Deployment](#aws-ec2-deployment)
4. [Google Cloud Run Deployment](#google-cloud-run-deployment)
5. [Azure Container Instances](#azure-container-instances)
6. [Nginx Reverse Proxy Setup](#nginx-reverse-proxy-setup)
7. [CI/CD Pipeline Setup](#cicd-pipeline-setup)
8. [Monitoring & Troubleshooting](#monitoring--troubleshooting)

---

# 🏠 Local Development Setup

## Step 1: Install Docker

### macOS
```bash
# Download Docker Desktop from:
# https://www.docker.com/products/docker-desktop

# Verify installation
docker --version
docker-compose --version
```

### Windows
```bash
# Download Docker Desktop from:
# https://www.docker.com/products/docker-desktop

# Open PowerShell (Admin) and verify:
docker --version
docker-compose --version
```

### Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install -y docker.io docker-compose

# Add your user to docker group (optional, to avoid sudo)
sudo usermod -aG docker $USER
newgrp docker

# Verify
docker --version
docker-compose --version
```

---

## Step 2: Clone Repository

```bash
git clone https://github.com/saipureddymanohar/Real-Time-Fake-Detection-System-Using-CNN-GAN.git
cd Real-Time-Fake-Detection-System-Using-CNN-GAN
```

---

## Step 3: Setup Environment File

```bash
# Copy example environment file
cp .env.example .env

# Edit environment variables (optional)
# nano .env  # or use your favorite text editor
```

**Default .env contents:**
```env
FLASK_ENV=production
FLASK_APP=appv2.py
FLASK_DEBUG=0
MAX_CONTENT_LENGTH=524288000
UPLOAD_FOLDER=uploads
SECRET_KEY=your-secret-key-here-change-in-production
WORKERS=2
TIMEOUT=120
```

---

## Step 4: Build Docker Image

```bash
# Build the image (takes 5-10 minutes first time)
docker-compose build

# Or build without cache (if you have issues)
docker-compose build --no-cache
```

**Expected output:**
```
Building deepfake-detection
Step 1/14 : FROM python:3.10-slim
...
Step 14/14 : CMD ["gunicorn", ...]
Successfully tagged deepfake-detection:latest
```

---

## Step 5: Run Application

```bash
# Start in foreground (see all logs)
docker-compose up

# Or start in background
docker-compose up -d

# View logs if running in background
docker-compose logs -f
```

**Expected output:**
```
deepfake-detection-app  | [2024-09-01 12:00:00 +0000] [1] [INFO] Starting gunicorn 21.2.0
deepfake-detection-app  | [2024-09-01 12:00:01 +0000] [7] [INFO] Booting worker with pid: 7
deepfake-detection-app  | [2024-09-01 12:00:02 +0000] [8] [INFO] Booting worker with pid: 8
```

---

## Step 6: Access Application

Open your browser:
```
http://localhost:5000
```

You should see the Deepfake Detection System interface!

---

## Step 7: Common Development Commands

```bash
# View running containers
docker-compose ps

# View real-time logs
docker-compose logs -f deepfake-detection

# Stop containers (keeps them)
docker-compose stop

# Stop and remove containers
docker-compose down

# Remove containers and volumes (delete all data)
docker-compose down -v

# Access container shell
docker-compose exec deepfake-detection /bin/bash

# Run Python command in container
docker-compose exec deepfake-detection python -c "import tensorflow; print(tensorflow.__version__)"
```

---

# 🌐 Production Deployment

## Step 1: Prepare for Production

### Update .env for Production
```bash
cp .env.example .env

# Edit .env and change these values:
FLASK_ENV=production
FLASK_DEBUG=0
SECRET_KEY=generate-a-random-secure-key-here
# Generate secure key:
# python -c "import secrets; print(secrets.token_hex(32))"
```

### Generate Secure Secret Key
```bash
# On Linux/macOS
python3 -c "import secrets; print(secrets.token_hex(32))"

# On Windows (PowerShell)
python -c "import secrets; print(secrets.token_hex(32))"

# Copy the output and paste in .env
```

---

## Step 2: Build Production Image

```bash
# Build production image
docker-compose -f docker-compose.prod.yml build
```

---

## Step 3: Run Production Container

```bash
# Start in background with automatic restart
docker-compose -f docker-compose.prod.yml up -d

# Verify it's running
docker-compose -f docker-compose.prod.yml ps

# View logs
docker-compose -f docker-compose.prod.yml logs -f
```

---

## Step 4: Verify Application

```bash
# Test health endpoint
curl http://localhost:5000/health

# Expected response: 200 OK
```

---

# ☁️ AWS EC2 Deployment

## Step 1: Launch EC2 Instance

### Via AWS Console

1. Go to https://console.aws.amazon.com/ec2
2. Click **Launch Instance**
3. Select **Ubuntu 22.04 LTS**
4. Choose instance type: **t3.large** (4GB RAM, 2 vCPU)
5. Configure storage: **30GB** (gp3)
6. Add tag: `Name: deepfake-detection`
7. Security group: Create new with these rules:
   - Type: SSH, Port: 22, Source: Your IP
   - Type: HTTP, Port: 80, Source: 0.0.0.0/0
   - Type: Custom TCP, Port: 5000, Source: 0.0.0.0/0 (or specific IP)
8. Review and launch
9. Download key pair (.pem file) and save securely

---

## Step 2: Connect to Instance

```bash
# Make key file readable (Linux/macOS)
chmod 400 your-key-pair.pem

# Connect via SSH
ssh -i your-key-pair.pem ubuntu@your-ec2-public-ip

# Example:
# ssh -i deepfake.pem ubuntu@54.123.456.789
```

---

## Step 3: Install Docker on EC2

```bash
# Update system
sudo apt update
sudo apt upgrade -y

# Install Docker
sudo apt install -y docker.io docker-compose

# Add ubuntu user to docker group
sudo usermod -aG docker ubuntu

# Apply group changes
newgrp docker

# Verify installation
docker --version
docker-compose --version
```

---

## Step 4: Deploy Application

```bash
# Clone repository
git clone https://github.com/saipureddymanohar/Real-Time-Fake-Detection-System-Using-CNN-GAN.git
cd Real-Time-Fake-Detection-System-Using-CNN-GAN

# Setup environment
cp .env.example .env

# Edit .env with production values
nano .env
# Change: FLASK_ENV=production, SECRET_KEY=your-secure-key

# Build image (takes 10-15 minutes)
docker-compose -f docker-compose.prod.yml build

# Start application in background
docker-compose -f docker-compose.prod.yml up -d

# Verify it's running
docker-compose -f docker-compose.prod.yml ps

# View logs
docker-compose -f docker-compose.prod.yml logs -f
```

---

## Step 5: Access Application

Open your browser:
```
http://your-ec2-public-ip:5000
```

Example: `http://54.123.456.789:5000`

---

## Step 6: Keep Application Running

### Option A: Use systemd service (Recommended)

Create service file:
```bash
sudo nano /etc/systemd/system/deepfake.service
```

Paste this content:
```ini
[Unit]
Description=Deepfake Detection System
After=docker.service
Requires=docker.service

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/Real-Time-Fake-Detection-System-Using-CNN-GAN
ExecStart=/usr/bin/docker-compose -f docker-compose.prod.yml up
ExecStop=/usr/bin/docker-compose -f docker-compose.prod.yml down
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Enable and start service:
```bash
sudo systemctl daemon-reload
sudo systemctl enable deepfake.service
sudo systemctl start deepfake.service
sudo systemctl status deepfake.service
```

---

### Option B: Use screen (Simple)

```bash
# Install screen
sudo apt install -y screen

# Create a screen session
screen -S deepfake

# Inside screen, run:
cd Real-Time-Fake-Detection-System-Using-CNN-GAN
docker-compose -f docker-compose.prod.yml up

# Detach from screen: Ctrl+A then D
# Reattach: screen -r deepfake
```

---

## Step 7: Setup Domain Name (Optional)

### Map Domain to EC2 IP

1. Go to your domain registrar (GoDaddy, Namecheap, etc.)
2. Create A record:
   - Type: A
   - Name: @ (or subdomain like deepfake)
   - Value: Your EC2 public IP
   - TTL: 3600

Wait 5-10 minutes for DNS to propagate, then access:
```
http://your-domain.com:5000
```

---

# 🌍 Google Cloud Run Deployment

## Step 1: Setup Google Cloud Project

```bash
# Install Google Cloud CLI
# Download from: https://cloud.google.com/sdk/docs/install

# Authenticate
gcloud auth login

# Set project ID
gcloud config set project YOUR_PROJECT_ID

# Enable Container Registry API
gcloud services enable run.googleapis.com
gcloud services enable containerregistry.googleapis.com
```

---

## Step 2: Configure Dockerfile for Cloud Run

The existing Dockerfile works, but add this to listen on PORT environment variable:

In `appv2.py`, find where Flask is created and modify:
```python
if __name__ == '__main__':
    port = int(os.environ.get('PORT', 5000))
    app.run(host='0.0.0.0', port=port)
```

---

## Step 3: Build and Push Image

```bash
# Set project ID variable
export PROJECT_ID=$(gcloud config get-value project)

# Build image
gcloud builds submit --tag gcr.io/$PROJECT_ID/deepfake-detection

# Verify image
gcloud container images list
```

---

## Step 4: Deploy to Cloud Run

```bash
gcloud run deploy deepfake-detection \
  --image gcr.io/$PROJECT_ID/deepfake-detection \
  --platform managed \
  --region us-central1 \
  --memory 4Gi \
  --timeout 600 \
  --allow-unauthenticated \
  --set-env-vars FLASK_ENV=production,SECRET_KEY=your-secret-key
```

---

## Step 5: Access Application

After deployment, you'll get a URL like:
```
https://deepfake-detection-xxxxxxxxxx-uc.a.run.app
```

---

## Step 6: View Logs

```bash
gcloud run logs read deepfake-detection --limit 50

# Follow logs in real-time
gcloud run logs read deepfake-detection --limit 50 --follow
```

---

# 🔵 Azure Container Instances

## Step 1: Install Azure CLI

```bash
# macOS
brew install azure-cli

# Linux
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Windows
# Download from: https://aka.ms/installazurecliwindows

# Verify
az --version
```

---

## Step 2: Login to Azure

```bash
az login

# Set subscription
az account set --subscription YOUR_SUBSCRIPTION_ID
```

---

## Step 3: Create Resource Group

```bash
az group create \
  --name deepfake-rg \
  --location eastus
```

---

## Step 4: Create Container Registry

```bash
az acr create \
  --resource-group deepfake-rg \
  --name deepfakeregistry \
  --sku Basic
```

---

## Step 5: Build and Push Image

```bash
# Login to registry
az acr login --name deepfakeregistry

# Build image
az acr build \
  --registry deepfakeregistry \
  --image deepfake-detection:latest \
  .

# Get registry URL
az acr show --resource-group deepfake-rg --name deepfakeregistry --query loginServer
```

---

## Step 6: Deploy Container Instance

```bash
REGISTRY_URL=$(az acr show --resource-group deepfake-rg --name deepfakeregistry --query loginServer -o tsv)

az container create \
  --resource-group deepfake-rg \
  --name deepfake-detection \
  --image $REGISTRY_URL/deepfake-detection:latest \
  --cpu 2 \
  --memory 4 \
  --ports 5000 \
  --environment-variables FLASK_ENV=production SECRET_KEY=your-secret-key
```

---

## Step 7: Get Public IP

```bash
az container show \
  --resource-group deepfake-rg \
  --name deepfake-detection \
  --query ipAddress.ip \
  --output tsv
```

Access at: `http://YOUR_IP:5000`

---

# 🔧 Nginx Reverse Proxy Setup

## Why Use Nginx?

- ✅ Better performance
- ✅ Load balancing
- ✅ SSL/HTTPS support
- ✅ Caching
- ✅ Access control

---

## Step 1: Install Nginx

```bash
sudo apt update
sudo apt install -y nginx
```

---

## Step 2: Create Nginx Configuration

```bash
sudo nano /etc/nginx/sites-available/deepfake
```

Paste this configuration:

```nginx
upstream deepfake_backend {
    server localhost:5000;
}

server {
    listen 80;
    server_name your-domain.com www.your-domain.com;
    client_max_body_size 500M;

    location / {
        proxy_pass http://deepfake_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }

    location /static/ {
        alias /home/ubuntu/Real-Time-Fake-Detection-System-Using-CNN-GAN/static/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    location /uploads/ {
        alias /home/ubuntu/Real-Time-Fake-Detection-System-Using-CNN-GAN/uploads/;
    }
}
```

---

## Step 3: Enable Configuration

```bash
# Create symbolic link
sudo ln -s /etc/nginx/sites-available/deepfake /etc/nginx/sites-enabled/deepfake

# Disable default site (optional)
sudo rm /etc/nginx/sites-enabled/default

# Test configuration
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx
```

---

## Step 4: Access Through Nginx

Now access your application via:
```
http://your-domain.com
```

Instead of:
```
http://your-domain.com:5000
```

---

## Step 5: Setup HTTPS with Let's Encrypt (Free SSL)

```bash
# Install Certbot
sudo apt install -y certbot python3-certbot-nginx

# Get SSL certificate (automatic)
sudo certbot --nginx -d your-domain.com -d www.your-domain.com

# Verify renewal
sudo certbot renew --dry-run
```

Now access via:
```
https://your-domain.com
```

---

# 🔄 CI/CD Pipeline Setup

## Automatic Testing and Deployment with GitHub Actions

### Step 1: Create GitHub Actions Workflow

Create `.github/workflows/deploy.yml`:

```yaml
name: Build and Deploy

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      
      - name: Build Docker image
        run: |
          docker-compose build
      
      - name: Test Docker image
        run: |
          docker-compose up -d
          sleep 5
          curl -f http://localhost:5000/health || exit 1
          docker-compose down
      
      - name: Push to Docker Hub (optional)
        if: github.event_name == 'push' && github.ref == 'refs/heads/main'
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      
      - name: Push image
        if: github.event_name == 'push' && github.ref == 'refs/heads/main'
        run: |
          docker tag deepfake-detection:latest ${{ secrets.DOCKER_USERNAME }}/deepfake-detection:latest
          docker push ${{ secrets.DOCKER_USERNAME }}/deepfake-detection:latest
```

### Step 2: Add GitHub Secrets

1. Go to Repository Settings → Secrets
2. Add these secrets:
   - `DOCKER_USERNAME`: Your Docker Hub username
   - `DOCKER_PASSWORD`: Your Docker Hub password

### Step 3: Automatic Deployment to EC2

Add to `.github/workflows/deploy.yml`:

```yaml
  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    
    steps:
      - name: Deploy to EC2
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ubuntu
          key: ${{ secrets.EC2_PRIVATE_KEY }}
          script: |
            cd ~/Real-Time-Fake-Detection-System-Using-CNN-GAN
            git pull origin main
            docker-compose -f docker-compose.prod.yml pull
            docker-compose -f docker-compose.prod.yml up -d
```

---

# 📊 Monitoring & Troubleshooting

## Common Issues and Solutions

### Issue: Container Won't Start

```bash
# Check logs
docker-compose logs deepfake-detection

# Rebuild without cache
docker-compose build --no-cache
docker-compose up

# Check if port 5000 is already in use
sudo lsof -i :5000
```

---

### Issue: Out of Memory

```bash
# Check memory usage
docker stats

# Increase Docker memory
# Docker Desktop: Preferences > Resources > Memory

# Or increase in docker-compose.prod.yml:
# deploy.resources.limits.memory: 8G
```

---

### Issue: Models Not Loading

```bash
# Check if models directory exists
docker-compose exec deepfake-detection ls -la models/

# Extract pre-trained models
cd models
7z x ../fake_audio_image_video_v4.7z
```

---

### Issue: Permission Denied

```bash
# Fix file permissions
sudo chown -R 1000:1000 ./uploads ./models
sudo chmod -R 755 ./uploads ./models

# Or inside container
docker-compose exec deepfake-detection chmod -R 755 /app/uploads
```

---

## Health Checks

```bash
# Test application health
curl http://localhost:5000/health

# Monitor in real-time
watch -n 1 'docker stats --no-stream'

# View detailed logs
docker-compose logs --tail=100 -f

# Check container status
docker-compose ps
```

---

## Backup and Restore

```bash
# Backup uploaded files
docker cp deepfake-detection-app:/app/uploads ./backup/uploads_$(date +%Y%m%d)

# Backup entire volumes
docker run --rm -v uploads_volume:/data -v $(pwd):/backup \
  alpine tar czf /backup/uploads.tar.gz /data

# Restore from backup
tar xzf uploads.tar.gz -C ./uploads
```

---

## Performance Optimization

```bash
# Increase Gunicorn workers (in Dockerfile)
CMD ["gunicorn", "--workers", "4", "--bind", "0.0.0.0:5000", "appv2:app"]

# Adjust for your CPU: 2-4 workers per core

# View current resource usage
docker stats deepfake-detection-app

# Increase container memory limit
# Edit docker-compose.prod.yml:
# deploy.resources.limits.memory: 8G
```

---

## Useful Commands Reference

```bash
# Build
docker-compose build
docker-compose build --no-cache

# Run
docker-compose up
docker-compose up -d
docker-compose -f docker-compose.prod.yml up -d

# Stop
docker-compose stop
docker-compose down
docker-compose down -v

# Logs
docker-compose logs
docker-compose logs -f
docker-compose logs --tail=50

# Access container
docker-compose exec deepfake-detection /bin/bash
docker-compose exec deepfake-detection python script.py

# Status
docker-compose ps
docker stats
docker inspect deepfake-detection-app

# Cleanup
docker-compose down -v
docker system prune -a
docker volume prune
```

---

## Support & Documentation

- 📚 [Docker Documentation](https://docs.docker.com/)
- 🌍 [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- ☁️ [Google Cloud Run Documentation](https://cloud.google.com/run/docs)
- 🔵 [Azure Container Instances Docs](https://docs.microsoft.com/en-us/azure/container-instances/)
- 🚀 [Flask Deployment Guide](https://flask.palletsprojects.com/deployment/)

---

## ✅ Deployment Checklist

Before going live:

- [ ] All Docker files created (Dockerfile, docker-compose.yml, etc.)
- [ ] Environment variables configured (.env)
- [ ] Application builds without errors
- [ ] Container starts and stays running
- [ ] Application accessible on port 5000
- [ ] Health check passing (`/health` endpoint)
- [ ] Uploads directory has proper permissions
- [ ] Resource limits configured
- [ ] Nginx reverse proxy setup (optional but recommended)
- [ ] HTTPS/SSL configured (for production)
- [ ] Monitoring/logging enabled
- [ ] Backup strategy in place
- [ ] Auto-restart policies configured

---

**Happy Deploying! 🚀**
