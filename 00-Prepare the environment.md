# Preapter the environment and Check

## Install and check
```
 sudo apt update
 sudo apt install -y   ca-certificates   curl   gnupg
 sudo install -m 0755 -d /etc/apt/keyrings
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
 sudo chmod a+r /etc/apt/keyrings/docker.gpg
 echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
   $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
 sudo apt update
 sudo apt install -y   docker-ce   docker-ce-cli   containerd.io   docker-buildx-plugin   docker-compose-plugin

sudo systemctl enable docker

sudo systemctl start docker
```
Verify
```
docker --version
docker compose version
docker info
```
## Create Docker Network

Traefik communicates through one shared network.

docker network create proxylab

Verify
```
docker network ls
```
