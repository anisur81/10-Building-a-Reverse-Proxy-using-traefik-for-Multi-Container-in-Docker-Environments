# Docker Compose Yaml File

```

version: '3.8'

networks:
  proxy-net:
    driver: bridge

services:
  # --- TRAEFIK REVERSE PROXY ---
  traefik:
    image: traefik:v3.6
    container_name: traefik-proxy
    restart: always
    environment:
      DOCKER_API_VERSION: "1.55"
    ports:
      - "80:80"       # Public HTTP entrypoint
      - "8080:8080"   # Traefik Web Dashboard port
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro # Allows Traefik to listen to Docker events
    command:
      - "--api.insecure=true"                   # Enables the dashboard without extra auth (dev only)
      - "--api.dashboard=true"                  # Spawns the web interface
      - "--providers.docker=true"               # Monitors the local Docker engine
      - "--providers.docker.exposedbydefault=false" # Requires explicit label activation
      - "--entrypoints.web.address=:80"         # Names port 80 entrypoint as 'web'
    networks:
      - proxy-net
    labels:
      - "traefik.enable=true"                   # Explicitly tells Traefik to look at this container
      - "traefik.http.routers.dashboard.rule=Host(`traefik.localhost`)" # Routing rule for dashboard
      - "traefik.http.routers.dashboard.service=api@internal"           # Tells proxy to map internally
      - "traefik.http.routers.dashboard.entrypoints=web"

  # --- APPLICATION CONTAINER 1 (HTTPD/Apache) ---
  web-app-one:
    image: httpd:alpine
    container_name: app-apache
    restart: always
    networks:
      - proxy-net
    labels:
      - "traefik.enable=true"                   # Exposes this app to Traefik
      - "traefik.http.routers.app1.rule=Host(`web1.localhost`)" # Domain-based routing rule
      - "traefik.http.routers.app1.entrypoints=web"
      - "traefik.http.services.app1.loadbalancer.server.port=80" # Apache listens inside on port 80

  # --- APPLICATION CONTAINER 2 (Node/Whoami App) ---
  web-app-two:
    image: traefik/whoami
    container_name: app-node-whoami
    restart: always
    networks:
      - proxy-net
    labels:
      - "traefik.enable=true"                   # Exposes this app to Traefik
      - "traefik.http.routers.app2.rule=Host(`web2.localhost`)" # Domain-based routing rule
      - "traefik.http.routers.app2.entrypoints=web"
      - "traefik.http.services.app2.loadbalancer.server.port=80" # Whoami listens inside on port 80

```
