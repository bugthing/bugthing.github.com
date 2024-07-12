---
layout: default
title: Home server compose yaml
---

Here is my compose.yaml I run on my raspberry pi 4 that is always available on my network

```
services:
  nginx-proxy:
    image: jc21/nginx-proxy-manager:latest
    networks:
      - rpinet
    restart: always
    ports:
      - 8880:80/tcp
      - 8443:443/tcp
      - 8881:81/tcp
    environment:
      DB_SQLITE_FILE: /data/database.sqlite
    volumes:
      - /home/pi/home-server/container-volumes/nginx-proxy/data:/data
      - /home/pi/home-server/container-volumes/nginx-proxy/letsencrypt:/etc/letsencrypt

  jellyfin:
    image: jellyfin/jellyfin
    networks:
      - rpinet
    restart: always
    ports:
      - 8096:8096/tcp
    devices:
      - /dev/dri/renderD128:/dev/dri/renderD128 # VAAPI devices, may be D128, D129, etc.
      - /dev/dri/card0:/dev/dri/card0
      - /dev/vchiq:/dev/qchiq  # Rpi4
    volumes:
      - /home/pi/home-server/container-volumes/jellyfin/config":/config
      - /home/pi/home-server/container-volumes/jellyfin/cache":/cache
      - /mnt/4TB/Video/Movies:/media/Movies
      - /mnt/4TB/Video/TV:/media/TV
      - /mnt/4TB/Video/Kids:/media/Kids
      - /mnt/4TB/Audio/All/:/media/Music

  cloudflared:
    image: visibilityspots/cloudflared
    networks:
      rpinet:
        ipv4_address: 172.18.0.20
    restart: always
    ports:
      - 5053:5053/tcp
      - 5053:5053/udp
      - 8082:8082/tcp
    environment:
      PORT: 5053
      METRICS: 0.0.0.0:8082

  pihole:
    image: pihole/pihole:latest
    networks:
      - rpinet
    restart: always
    ports:
      - 53:53/tcp
      - 53:53/udp
      - 67:67/udp
      - 8081:80/tcp
      - 4381:443/tcp
    environment:
      WEBPASSWORD: boomgoatwirestop
    volumes:
      - /home/pi/home-server/container-volumes/pihole/etc:/etc/pihole
      - /home/pi/home-server/container-volumes/pihole/dnsmasq.d:/etc/dnsmasq.d
      - /etc/localtime:/etc/localtime

  thin-site:
    build:
      context: /home/pi/src/contained-jekyll-site
      dockerfile: /home/pi/src/contained-jekyll-site/Dockerfile
    networks:
      - rpinet
    ports:
      - 4000:80/tcp

networks:
  rpinet:
    ipam:
      driver: default
      config:
        - subnet: 172.18.0.0/16
```
