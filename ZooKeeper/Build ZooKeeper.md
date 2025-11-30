```bash
docker run --pid=host --net=host --privileged -t --rm \
-w `pwd` -v `pwd`:`pwd` -v home-m2:/root/.m2 -v home-ant:/root/.ant registry.smtx.io/zookeeper/builder:oe1 \
bash -c 'git config --global --add safe.directory "*" && make rpm && find ~/rpmbuild/RPMS/x86_64/ -type f -exec curl -T "{}" http://192.168.91.19:81/testing/zookeeper/oe1/x86_64/ \;'
```

```bash
docker run --pid=host --net=host --privileged -t --rm \
-w `pwd` -v `pwd`:`pwd` -v home-m2:/root/.m2 -v home-ant:/root/.ant registry.smtx.io/zookeeper/builder:el7-amd64 \
bash -c 'git config --global --add safe.directory "*" && make rpm && find ~/rpmbuild/RPMS/x86_64/ -type f -exec curl -T "{}" http://192.168.91.19:81/testing/zookeeper/el7/x86_64/ \;'
```

```bash
docker run --pid=host --net=host --privileged -t --rm \
  -w `pwd` -v `pwd`:`pwd` -v home-m2:/root/.m2 -v home-ant:/root/.ant registry.smtx.io/zookeeper/builder:oe1 \
  bash -c 'git config --global --add safe.directory "*" && make rpm && find ~/rpmbuild/RPMS/aarch64/ -type f -exec curl -T "{}" http://192.168.31.231:5000/f/testing/zookeeper/oe1/aarch64/ \;'
```

```bash
docker run --pid=host --net=host --privileged -t --rm \
  -w `pwd` -v `pwd`:`pwd` -v home-m2:/root/.m2 -v home-ant:/root/.ant registry.smtx.io/zookeeper/builder:el7-arm64v8 \
  bash -c 'git config --global --add safe.directory "*" && make rpm && find ~/rpmbuild/RPMS/aarch64/ -type f -exec curl -T "{}" http://192.168.31.231:5000/f/testing/zookeeper/el7/aarch64/ \;'
```
