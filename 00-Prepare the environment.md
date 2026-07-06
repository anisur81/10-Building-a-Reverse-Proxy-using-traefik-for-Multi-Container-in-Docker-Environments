# Preapter the environment and Check

## Install and check
```
sudo apt update

sudo apt install docker.io docker-compose-plugin -y

sudo systemctl enable docker

sudo systemctl start docker
```
Verify
```
docker --version
docker compose version
```
## Create Docker Network

Traefik communicates through one shared network.

docker network create proxylab

Verify
```
docker network ls
```
