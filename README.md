# 🐋 MinIO with Nexus Docker Registry

A complete Docker setup featuring **Nexus Repository Manager** with integrated Docker registry support, powered by **Traefik** reverse proxy for SSL termination and domain routing.

## 📋 Table of Contents

- [Features](#-features)
- [Prerequisites](#-prerequisites)
- [Quick Start](#-quick-start)
- [Configuration](#-configuration)
- [Docker Registry Setup](#-docker-registry-setup)
- [Usage](#-usage)
- [Troubleshooting](#-troubleshooting)

## ✨ Features

- 🏗️ **Nexus Repository Manager 3** for artifact management
- 🐳 **Private Docker Registry** with SSL support
- 🌐 **Traefik Reverse Proxy** with automatic HTTPS
- 🔒 **Let's Encrypt SSL** certificates
- 📦 **Volume persistence** for data safety

## 🔧 Prerequisites

- Docker and Docker Compose installed
- A domain name pointing to your server (for SSL certificates)
- Ports 80 and 443 available

## 🚀 Quick Start

### 1. Clone and Setup

```bash
git clone <repository-url>
cd MinIO-with-Nexus
```

### 2. Environment Configuration

Copy the template environment file and configure it:

```bash
cp template.env .env
```

Edit `.env` with your details:
```env
MINIO_ROOT_USER=your-minio-user
MINIO_ROOT_PASSWORD=your-secure-password
EMAIL=your-email@domain.com
DOMAIN=yourdomain.com
```

### 3. Launch Services

```bash
docker-compose up -d
```

## ⚙️ Configuration

### Nexus Initial Setup

1. **Wait for Nexus to start** (this may take a few minutes)

2. **Retrieve admin password:**
   ```bash
   cat nexus-data/admin.password
   ```

3. **Access Nexus UI:**
   - URL: `https://nexus.yourdomain.com` (or `http://localhost:8081` for local testing)
   - Username: `admin`
   - Password: Use the password from step 2

## 🐳 Docker Registry Setup

### 1. Create Docker Repository

1. Navigate to **Administration** → **Repositories**
2. Click **Create repository** → **docker (hosted)**
3. Configure with these settings:
   - **Name:** `docker-registry`
   - **HTTP Port:** `8085`

### 2. Test Docker Registry Access

```bash
# Login to your private registry
docker login docker.yourdomain.com

# For local testing
docker login docker.localhost:8085
```

## 📦 Usage

### Pushing Images

```bash
# Tag your image
docker tag my-app:latest docker.yourdomain.com/my-app:latest

# Push to registry
docker push docker.yourdomain.com/my-app:latest
```

### Pulling Images

```bash
# Pull from your private registry
docker pull docker.yourdomain.com/my-app:latest
```

### Creating Images from Containers

```bash
# Commit running container as new image
docker commit my-container docker.yourdomain.com/my-app:v1.0

# Push the committed image
docker push docker.yourdomain.com/my-app:v1.0
```

## 🔧 Troubleshooting

### Common Issues

**Nexus not starting:**
- Check if port 8081 is available
- Verify Docker has enough memory allocated

**SSL Certificate issues:**
- Ensure your domain points to the server
- Check Traefik logs: `docker-compose logs traefik`

**Docker login fails:**
- Verify Docker Bearer Token Realm is enabled
- Check if repository is configured correctly

### Useful Commands

```bash
# View all service logs
docker-compose logs

# View specific service logs
docker-compose logs nexus
docker-compose logs traefik

# Restart services
docker-compose restart

# Stop all services
docker-compose down
```

## 📁 Project Structure

```
MinIO-with-Nexus/
├── docker-compose.yml      # Main service definitions
├── docker-compose-dev.yml  # Development overrides
├── traefik.yml            # Traefik configuration
├── template.env           # Environment template
├── nexus-data/           # Nexus persistent data
├── minio-data/           # MinIO persistent data
└── acme.json             # SSL certificates
```

## 🤝 Contributing

Feel free to submit issues and pull requests to improve this setup!

## 📄 License

This project is open source and available under the [MIT License](LICENSE).