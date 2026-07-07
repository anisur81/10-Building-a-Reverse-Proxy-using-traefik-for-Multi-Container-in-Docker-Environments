# Traefik Reverse Proxy Configuration with Docker Compose

## 📋 Overview
This Docker Compose configuration sets up a **Traefik reverse proxy** with two backend applications running in containers. Traefik acts as the entry point for all HTTP traffic and routes requests based on domain names.
---

## 🏗️ Architecture
```
Internet → Port 80 → Traefik (v3.6) → Routes to:
                                   ├── web-app-one (Apache on port 80)
                                   └── web-app-two (whoami on port 80)
```
---

## 🚀 Services Overview

| Service | Image | Container Name | Purpose | Internal Port |
|---------|-------|----------------|---------|---------------|
| **Traefik** | `traefik:v3.6` | `traefik-proxy` | Reverse Proxy / Load Balancer | 80, 8080 (dashboard) |
| **Web App One** | `httpd:alpine` | `app-apache` | Apache Web Server | 80 |
| **Web App Two** | `traefik/whoami` | `app-node-whoami` | Debug/Testing Application | 80 |
---

## 🔧 Configuration Details

### Network
- **Network Name**: `proxy-net`
- **Driver**: `bridge`
- **Purpose**: Isolated network for inter-container communication

### Traefik Configuration
```yaml
Entrypoints:
  - web: :80 (HTTP)

Providers:
  - Docker (with explicit label activation)

Dashboard:
  - Enabled (insecure mode - development only)
  - URL: http://traefik.localhost
```

---

## 🌐 Routing Rules

| Domain | Service | Port | Description |
|--------|---------|------|-------------|
| `traefik.localhost` | `api@internal` | 80 | Traefik Dashboard |
| `web1.localhost` | `web-app-one` | 80 | Apache Web Server |
| `web2.localhost` | `web-app-two` | 80 | whoami Debug Service |

---

## 🛠️ Quick Start

### Prerequisites
- Docker Engine 20.10+
- Docker Compose 2.0+

### 1. Start Services
```bash
docker compose up -d
```

### 2. Verify Running Containers
```bash
docker compose ps
```

### 3. Access Services
| Service | URL |
|---------|-----|
| Traefik Dashboard | http://traefik.localhost |
| Apache Web Server | http://web1.localhost |
| whoami Debug | http://web2.localhost |

### 4. View Logs
```bash
# All services
docker compose logs

# Specific service
docker compose logs traefik
docker compose logs web-app-one
```

### 5. Stop Services
```bash
docker compose down
```

### 6. Stop and Remove Volumes
```bash
docker compose down -v
```

---

## 📝 Key Features

### ✅ Automatic Service Discovery
- Traefik watches Docker for new containers
- No need to restart proxy when adding services

### ✅ Load Balancing
- Built-in load balancing capabilities
- Supports multiple instances of same service

### ✅ Labels-Based Configuration
- Services declare routing rules through Docker labels
- Decouples configuration from application code

### ✅ Container Health Checks
- Automatic detection of healthy/unhealthy containers
- Removes unhealthy instances from rotation

---

## 🔒 Security Considerations

### ⚠️ Development Only Configuration
```yaml
# This configuration is NOT production-ready:
- "--api.insecure=true"  # No authentication on dashboard
- No HTTPS/SSL configuration
- No rate limiting
- No access logging configured
```

### 🔐 Production Recommendations
1. **Enable HTTPS** with Let's Encrypt or manual certificates
2. **Secure Dashboard** with authentication middleware
3. **Add Rate Limiting** middleware
4. **Configure Access Logs** for audit purposes
5. **Remove Insecure API Mode** → `--api.insecure=false`

---

## 🧪 Testing the Setup

### Test Service 1 (Apache)
```bash
curl -H "Host: web1.localhost" http://localhost
```

### Test Service 2 (whoami)
```bash
curl -H "Host: web2.localhost" http://localhost
```

### Test Traefik Dashboard
```bash
curl -H "Host: traefik.localhost" http://localhost
```

---

## 🚨 Troubleshooting

### Container Not Starting
```bash
# Check logs
docker-compose logs traefik

# Check port availability
sudo netstat -tulpn | grep :80
```

### Routing Not Working
```bash
# Verify container is running
docker ps

# Check if labels are applied
docker inspect app-apache | grep traefik
```

### Docker Socket Permission Issues
```bash
# Add user to docker group (Linux)
sudo usermod -aG docker $USER
# Restart session
```

---

## 📊 Monitoring

### Traefik Dashboard Access
- **URL**: http://traefik.localhost
- **Status**: Shows active routers, services, and health
- **Metrics**: Shows request counts and response status

---

## 📌 Additional Configuration Examples

### Add HTTPS Support (Let's Encrypt)
```yaml
traefik:
  command:
    - "--certificatesresolvers.le.acme.email=you@email.com"
    - "--certificatesresolvers.le.acme.tlschallenge=true"
    - "--entrypoints.websecure.address=:443"
  ports:
    - "443:443"
  labels:
    - "traefik.http.routers.app1.entrypoints=websecure"
    - "traefik.http.routers.app1.tls.certresolver=le"
```

### Add Authentication (Basic Auth)
```yaml
labels:
  - "traefik.http.middlewares.auth.basicauth.users=admin:$$2y$$10$$5S6hX/jFk..." # bcrypt hash
  - "traefik.http.routers.dashboard.middlewares=auth"
```

---

## 🧹 Cleanup

```bash
# Stop all containers
docker-compose down

# Remove all containers, networks, and volumes
docker-compose down -v

# Remove images
docker rmi traefik:v3.6 httpd:alpine traefik/whoami
```

 
