---
layout: default
title: Home CCTV compose yaml
---

Here is my compose.yaml I run on my raspberry pi 3 that is always available on my network and acts
as a home CCTV system.

```
services:
  cctv:
    build:
      context: /home/pi/src/Shinobi
      dockerfile: /home/pi/src/Shinobi/Dockerfile
    image: shinobi:1
    restart: always
    ports:
      - "8880:8080/tcp"
    volumes:
        - /dev/shm/Shinobi/streams:/dev/shm/streams:rw
        - /mnt/1TB/container-volumes/shinobi/config:/config:rw
        - /mnt/1TB/container-volumes/shinobi/customAutoLoad:/home/Shinobi/libs/customAutoLoad:rw
        - /mnt/1TB/container-volumes/shinobi/database:/var/lib/mysql:rw
        - /mnt/1TB/container-volumes/shinobi/videos:/home/Shinobi/videos:rw
        - /mnt/1TB/container-volumes/shinobi/plugins:/home/Shinobi/plugins:rw
        - /etc/localtime:/etc/localtime:ro
```
