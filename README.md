# Bitwarden
  
<p align="left">
  <a href="https://github.com/vdarkobar/Home_Cloud#small-home-cloud">Home</a> |
  <a href="https://github.com/vdarkobar/NextCloud#nextcloud">NextCloud</a> |
  <a href="https://github.com/vdarkobar/WordPress#wordpress">WordPress</a> |
  <a href="https://github.com/vdarkobar/Ghost-blog#ghost-blog">Ghost-blog</a> |
  <a href="https://github.com/vdarkobar/Portainer">Joomla</a>  
  <br><br>
</p>  
  
Login to <a href="https://dash.cloudflare.com/">CloudFlare</a>  and add: *bw* Subdomain, pointing to your root Domain.
```
    A | example.com | YOUR WAN IP
```
```
    CNAME | bw | @ (or example.com)
```

---

### Create Docker networks:
```
sudo docker network create bw
```
### Clone Bitwarden Git Repository:
```
RED='\033[0;31m'; echo -ne "${RED}Enter directory name: "; read NAME; mkdir -p "$NAME"; \
cd "$NAME" && git clone https://github.com/vdarkobar/Bitwarden.git .
```
  
#### *Decide what you will use for*:
```
Domain name,
Bitwarden Port Number.
```

### Select and run all at once. Enter required data:
*Only works once, on wrong data input delete folder and clone again*.
```
RED='\033[0;31m'
echo -ne "${RED}Enter Domain name: "; read DNAME; \
echo -ne "${RED}Enter Bitwarden Port Number (BWPORTN:80): "; read BWPORTN; \
sed -i "s|01|${DNAME}|" .env && \
sed -i "s|02|${BWPORTN}|" .env && \
rm README.md && \
TOKEN=$(openssl rand -base64 48); sed -i "s|CHANGE_ADMIN_TOKEN|${TOKEN}|" .env
```
  
#### Command will add *ADMIN_TOKEN* to *.env* file, log in at:
```
https://bw.example.com/admin
```
  
### Start:
```
sudo docker-compose up -d
```
### Log:
```
sudo docker-compose logs bitwardenrs
sudo docker logs -tf --tail="50" bitwardenrs
```
  
### Dynamic config *(Traefik VM)*:
Create file: *service_name.yml* in Traefik: */data/configurations/* folder for routing and to get a free SSL certificate.
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
      rule: "Host(`bw.example.com`) && Path(`/notifications/hub`)" # adjust Domain name

    # Bitwarden service router
    bitwarden-service:
      service: bitwarden-service
      middlewares:
      entryPoints:
        - "websecure"
      rule: "Host(`bw.example.com`)" # adjust domain name


  # All services:
  services:
  
    # Bitwarden Websocket service
    bitwarden-websocket:
      loadBalancer:
        servers:
          - url: "http://local-ip:3012" # adjust ip

    # Bitwarden service
    bitwarden-service:
      loadBalancer:
        servers:
          - url: "http://local-ip:BWPORTN" # adjust ip and port nummber

```
### Middlewares *(Traefik VM)*:
Add to: *middlewares.yml* in Traefik: */data/configurations/*
```
http:

  # All middlewares:
  middlewares:
  
    # Websocket Bitwarden
    bw-stripPrefix:
      stripPrefix:
        prefixes:
          - "/notifications/hub"
        forceSlash: false
```
  
### Bitwarden: <i><a href="https://github.com/dani-garcia/bitwarden_rs/wiki">Features</a></i>
  
  
<p align="center">  
<a href="https://github.com/vdarkobar/Bitwarden#bitwarden">top of the page</a>
</p>
