```bash
docker volume create filebrowser-db
docker volume create filebrowser-config

docker rm $(docker stop filebrowser)

docker run --net host --restart=always \
-it -d --name filebrowser \
-v /mnt/uofs2:/srv \
-v filebrowser-config:/config \
-v filebrowser-db:/database \
-e PUID=$(id -u) \
-e PGID=$(id -g) \
filebrowser/filebrowser:s6
```