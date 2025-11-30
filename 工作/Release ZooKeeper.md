```bash
docker pull registry.smtx.io/zookeeper/builder:el7-amd64
docker volume create home-ant
docker volume create home-m2
docker run --pid=host --net=host --privileged -t --rm \
  -w `pwd` -v `pwd`:`pwd` -v home-m2:/root/.m2 -v home-ant:/root/.ant registry.smtx.io/zookeeper/builder:el7-amd64 \
  bash -c 'make rpm && find ~/rpmbuild/RPMS/x86_64/ -type f -exec curl -T "{}" http://192.168.91.19:81/testing/zookeeper/el7/x86_64/ \;'
```
