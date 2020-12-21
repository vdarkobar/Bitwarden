# Bitwarden
## deploy Bitwarden

##### Create docker networks (*if not already created*)
```
sudo docker network create bw
```
### Clone this git repository:
```
git clone https://vdarkobar:2211620c9da5dab0c7bb77e9aeb02087d293b293@github.com/vdarkobar/Bitwarden.git
```

##### Change domain name in multiple files
```
sudo nano Bitwarden/docker-compose.yml
```
##### Create new file for Traefik dynamic configuration (or add to your dynamic.yml)
```
http:
  routers:
  #
    bitwarden-ws:
      service: bitwarden-websocket
      middlewares:
        - bw-stripPrefix@file
      entryPoints:
        - "websecure"
      rule: "Host(`bw.domain.com`) && Path(`/notifications/hub`)"

    bitwarden-ws:
      service: bitwarden-service
      middlewares:
      entryPoints:
        - "websecure"
      rule: "Host(`bw.domain.com`)"

  services:
  #
    bitwarden-websocket:
      loadBalancer:
        servers:
          - url: "http://server_ip:3012"

    bitwarden-service:
      loadBalancer:
        servers:
          - url: "http://server_ip:8686" # adjust port nummber
#
```
##### Middleware (add to dynamic.yml)
```
http:
  middlewares:
#
    bw-stripPrefix:
      stripPrefix:
        prefixes:
          - "/notifications/hub"
        forceSlash: false
#
```

##### Start
```
sudo docker-compose -f Bitwarden/docker-compose.yml up -d
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
