```
docker run --name=homarr -d \
-v=/root/data/homarr/configs:/app/data/configs \
-v=/root/data/homarr/icons:/app/public/icons \
-v=/root/data/homarr/data:/data \
-v=/var/run/docker.sock:/var/run/docker.sock \
-w=/app \
-p 7575:7575 \
--restart=unless-stopped \
registry.smtx.io/ghcr.io/ajnart/homarr:latest node server.js
```
