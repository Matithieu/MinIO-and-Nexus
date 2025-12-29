# 💎 Nexus Docker Registry with Traefik

A production-ready Docker setup featuring **Nexus Repository Manager** with integrated Docker registry support, powered by **Traefik** reverse proxy for SSL termination and domain routing.

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
- 🌐 **Traefik v3 Reverse Proxy** with automatic HTTPS
- 🔒 **Let's Encrypt SSL** certificates with automatic renewal
- ⏱️ **Extended timeouts** (3 minutes) for large uploads/downloads
- 📦 **Volume persistence** for data safety
- 🎯 **Single docker-compose.yml** configuration

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
EMAIL=your-email@domain.com
DOMAIN=yourdomain.com
```

### 3. Prepare SSL Certificate File

```bash
touch acme.json
chmod 600 acme.json
```

### 4. Launch Services

```bash
docker-compose up -d
```

## ⚙️ Configuration

### Traefik Timeouts

The setup includes extended timeouts (3 minutes) to handle large file uploads and downloads:
- **Read timeout:** 180s
- **Write timeout:** 180s  
- **Idle timeout:** 180s

These are configured directly in [docker-compose.yml](docker-compose.yml) and help prevent "Client Closed Request" errors during large operations.

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

# For development/local testing
docker login docker.localhost
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
- Check permissions on nexus-data directory

**SSL Certificate issues:**
- Ensure your domain points to the server's public IP
- Verify ports 80 and 443 are accessible from the internet
- Check Traefik logs: `docker-compose logs traefik`
- Ensure acme.json has correct permissions (600)

**Docker login fails:**
- Verify Docker Bearer Token Realm is enabled in Nexus
- Check if repository is configured correctly on port 8085
- Ensure SSL certificate is valid

**Client Closed Request errors:**
- The timeouts are already set to 3 minutes
- For larger operations, consider increasing timeouts in docker-compose.yml
- Check network stability between client and server

### Useful Commands

```bash
# View all service logs
docker-compose logs

# View specific service logs
docker-compose logs nexus
docker-compose logs traefik

# Follow logs in real-time
docker-compose logs -f

# Restart services
docker-compose restart

# Stop all services
docker-compose down

# Stop and remove volumes (WARNING: deletes data)
docker-compose down -v
```

## 📁 Project Structure

```
MinIO-with-Nexus/
├── docker-compose.yml      # Main service definitions with Traefik config
├── docker-compose-dev.yml  # Development overrides
├── template.env            # Environment template
├── acme.json              # SSL certificates (auto-generated)
├── nexus-data/            # Nexus persistent data (auto-created)
└── README.md              # This file
```

## 🔐 Security Notes

- Change default Nexus admin password immediately after first login
- Keep your `.env` file secure and never commit it to version control
- The `acme.json` file contains SSL certificates and must have 600 permissions
- Consider using Docker secrets for production environments
- Enable Nexus security features like realms and user authentication

## 🚀 Production Deployment

For production use:
1. Use strong passwords and secure authentication
2. Configure regular backups of `nexus-data/`
3. Monitor disk space usage
4. Set up proper firewall rules
5. Consider using Docker Swarm or Kubernetes for high availability
6. Enable Nexus backup tasks
7. Configure log rotation

## 🤝 Contributing

Feel free to submit issues and pull requests to improve this setup!

## 📄 License

This project is open source and available under the [MIT License](LICENSE).