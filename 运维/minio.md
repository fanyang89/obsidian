```bash
docker run \
   -p 9000:9000 \
   -p 9001:9001 \
   --ulimit nofile=1024:1024 \
   --user $(id -u):$(id -g) \
   --name minio -d --init \
   -e "MINIO_ROOT_USER=fanmi" \
   -e "MINIO_ROOT_PASSWORD=DFCGVHNETUkvgHVehVbcEtkFJiuBDKvTJnrHbg" \
   -v minio-data:/data \
   quay.io/minio/minio server /data --console-address ":9001"
```
