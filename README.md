# Bitwarden
  
<p align="left">
  <a href="https://github.com/vdarkobar/Home_Cloud#proxmox">Home</a>
</p>  
  
### Create docker networks
```
sudo docker network create bw
```
### Clone this git repository
```
RED='\033[0;31m'; echo -ne "${RED}Enter directory name: "; read NAME; mkdir -p "$NAME"; \
cd "$NAME" && git clone https://github.com/vdarkobar/Bitwarden.git .
```
### Change ADMIN_TOKEN 
*Only works once, use bash
```
TOKEN=$(openssl rand -base64 48); sed -i "s|CHANGE_ADMIN_TOKEN|${TOKEN}|" .env
```
### Adjust if necessary
```
sudo nano .env
```
  
### Dynamic config *(Traefik VM)*
```
http:

  # All routers:
  routers:
  
    # Bitwarden Websocket router
    bitwarden-ws:
      service: bitwarden-websocket
      middlewares:
        - bw-stripPrefix@file
      entryPoints:
        - "websecure"
      rule: "Host(`bw.example.com`) && Path(`/notifications/hub`)" # adjust domain

    # Bitwarden service router
    bitwarden-ws:
      service: bitwarden-service
      middlewares:
      entryPoints:
        - "websecure"
      rule: "Host(`bw.example.com`)" # adjust domain


  # All services:
  services:
  
    # Bitwarden Websocket service
    bitwarden-websocket:
      loadBalancer:
        servers:
          - url: "http://local-ip:3012" # adjust ip (port fixed)

    # Bitwarden service
    bitwarden-service:
      loadBalancer:
        servers:
          - url: "http://local-ip:8686" # adjust ip and port nummber

```
##### Middlewares 
```
http:
  # All middlewares:
  middlewares:
  
    # Websocket middleware
    bw-stripPrefix:
      stripPrefix:
        prefixes:
          - "/notifications/hub"
        forceSlash: false
```
##### Start
```
sudo docker-compose up -d
```
##### Log
```
sudo docker-compose logs bitwardenrs
sudo docker logs -tf --tail="50" bitwardenrs
```
  
### Bitwarden [Features](https://github.com/dani-garcia/bitwarden_rs/wiki)
