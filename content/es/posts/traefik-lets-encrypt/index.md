+++
title = "Traefik, Let's Encrypt y https redirect"
date = "2025-08-29T19:49:54-03:00"
author = "Alitux"
authorTwitter = "" #do not include @
cover = ""
tags = ["traefik", "letsencrypt", "docker","linux"]
keywords = ["traefik", "letsencrypt", "docker","linux"]
description = "Hoy les paso rápidamente el docker-compose de traefik y letsencrypt con redireciconamiento de https a http"
showFullContent = false
readingTime = true
hideComments = false
+++

Si querés servir contenedores con Traefik y tener HTTPS automático con Let's Encrypt, esto es lo mínimo que necesitás:

---

## Docker Compose mínimo

```yaml

networks:
  traefik:
    external: true

services:
  traefik:
    image: traefik:v3.5
    container_name: traefik
    command:
      - "--api.insecure=true"
      - "--providers.docker=true"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--entrypoints.web.http.redirections.entrypoint.to=websecure"
      - "--entrypoints.web.http.redirections.entrypoint.scheme=https"
      - "--certificatesresolvers.letsencrypt.acme.httpchallenge=true"
      - "--certificatesresolvers.letsencrypt.acme.httpchallenge.entrypoint=web"
      - "--certificatesresolvers.letsencrypt.acme.email=hola@example.com"
      - "--certificatesresolvers.letsencrypt.acme.storage=/letsencrypt/acme.json"
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./letsencrypt:/letsencrypt
    networks:
      - traefik

  whoami:
    image: traefik/whoami
    container_name: whoami
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.whoami.rule=Host(\"example.com\")"
      - "traefik.http.routers.whoami.entrypoints=websecure"
      - "traefik.http.routers.whoami.tls=true"
      - "traefik.http.routers.whoami.tls.certresolver=letsencrypt"
    networks:
      - traefik
```
## Uso

1. Creamos la red de traefik: 

```bash
docker network create traefik
```
Levantamos el servicio de ejemplo:
2. Levantamos el docker compose:

```bash
docker compose up -d
```
3. Probamos que todo esté funcionando bien:
```bash
curl -vk https://example.com
```

Obvio que el whoami se puede borrar, yo lo dejo por motivos didácticos. No es necesario levantar un traefik por cada servicio que quieras servir, con un solo traefik basta y sobra. Supongamos que el whoami está en otro lado, quedaría así: 
```yaml
services:
  whoami:
    image: traefik/whoami
    container_name: whoami
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.whoami.rule=Host(\"example.com\")"
      - "traefik.http.routers.whoami.entrypoints=websecure"
      - "traefik.http.routers.whoami.tls=true"
      - "traefik.http.routers.whoami.tls.certresolver=letsencrypt"
    networks:
      - traefik
```
