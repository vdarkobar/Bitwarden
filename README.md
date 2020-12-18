# Bitwarden
## deploy Bitwarden behind Traefik

##### Create docker network
```
sudo docker network create proxy
```

### Clone this git repository:
```
git clone https://vdarkobar:2211620c9da5dab0c7bb77e9aeb02087d293b293@github.com/vdarkobar/Bitwarden.git
```

##### Change domain name
```
sudo nano Bitwarden/docker-compose.yml
```
##### Middleware (add to dynamic.yml)
```
bw-stripPrefix middleware for Websocket. This middleware will be added in dynamic.yml as below:
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
sudo docker logs -tf --tail="50" Bitwarden
```

##### Admin Page
```
# https://github.com/dani-garcia/bitwarden_rs/wiki
# ADMIN_TOKEN=
openssl rand -base64 48
```
