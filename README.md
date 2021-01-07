# Bitwarden
  
<p align="left">
  <a href="https://github.com/vdarkobar/Home_Cloud#proxmox">Home</a>
</p>  
  
Login to <a href="https://dash.cloudflare.com/">CloudFlare</a>  and add: *bw* subdomain, pointing to your root domain.
```
    CNAME | bw | @ (or example.com)
```

---

### Create Docker networks:
```
sudo docker network create bw
```
### Clone Bitwarden git repository:
```
RED='\033[0;31m'; echo -ne "${RED}Enter directory name: "; read NAME; mkdir -p "$NAME"; \
cd "$NAME" && git clone https://github.com/vdarkobar/Bitwarden.git .
```
  
#### *Decide what you will use for*:
```
Domain name,
# Command will add ADMIN_TOKEN to .env file.
```
### Select and run all at once. Enter required data:
*Only works once, use bash*
```
RED='\033[0;31m'
echo -ne "${RED}Enter Domain name: "; read DNAME; \
sed -i "s|01|${DNAME}|" .env && \
TOKEN=$(openssl rand -base64 48); sed -i "s|CHANGE_ADMIN_TOKEN|${TOKEN}|" .env
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
create file: service_name.yml in Traefik: /data/configurations/ folder for routing and to get a free SSL certificate.
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
### Middlewares *(Traefik VM)*:
Add to: middlewares.yml in Traefik: /data/configurations/
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
  
### Bitwarden: [Features](https://github.com/dani-garcia/bitwarden_rs/wiki)
