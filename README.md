# Bitwarden
## deploy Bitwarden

##### Create docker networks (*if not already created*)
```
sudo docker network create bw
```
### Clone this git repository:
```
echo -n "Enter directory name: "; read NAME; mkdir -p "$NAME"; cd "$NAME" \
&& git clone https://vdarkobar:2211620c9da5dab0c7bb77e9aeb02087d293b293@github.com/vdarkobar/Bitwarden.git .
```

##### Change domain name in multiple files
```
sudo nano docker-compose.yml
```
##### Create new file for Traefik dynamic configuration, or add to your dynamic.yml (site specific files)
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
      rule: "Host(`bw.domain.com`) && Path(`/notifications/hub`)"

    # Bitwarden service router
    bitwarden-ws:
      service: bitwarden-service
      middlewares:
      entryPoints:
        - "websecure"
      rule: "Host(`bw.domain.com`)"

  # All services:
  services:
  
    # Bitwarden Websocket service
    bitwarden-websocket:
      loadBalancer:
        servers:
          - url: "http://server_ip:3012"

    # Bitwarden service
    bitwarden-service:
      loadBalancer:
        servers:
          - url: "http://server_ip:8686" # adjust port nummber
#
```
##### Middleware for Websocket (add to dynamic.yml)
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
sudo docker logs -tf --tail="50" bitwardenrs
```
##### Enable Admin Page
```
# https://github.com/dani-garcia/bitwarden_rs/wiki
# ADMIN_TOKEN=
openssl rand -base64 48
```
