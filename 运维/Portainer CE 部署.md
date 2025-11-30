```yml
version: "3"
services:
  portainer-ce:
    ports:
      - 8000:8000
      - 9443:9443
    container_name: portainer
    restart: always
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    image: portainer/portainer-ce:2.21.4
volumes:
  portainer_data:
    external:
      name: portainer_data
```
