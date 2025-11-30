```dockerfile
FROM openeuler/openeuler:20.03-lts-sp3
RUN sed -i 's/http:\/\/repo.openeuler.org\/openEuler-20.03-LTS-SP3/https:\/\/mirrors.nju.edu.cn\/openeuler\/openEuler-20.03-LTS-SP3/g' /etc/yum.repos.d/openEuler.repo && dnf makecache

RUN dnf install -y http://192.168.17.20/repo/pub/openeuler/oe2003/smartx-iso-common/aarch64/Packages/python-gevent-20.6.2-1.aarch64.rpm http://192.168.17.20/repo/pub/openeuler/oe2003/smartx-iso-common/aarch64/Packages/python-greenlet-1.1.1-1.aarch64.rpm http://192.168.17.20/repo/pub/openeuler/oe2003/smartx-iso-common/noarch/Packages/python-zope.event-4.5.0-1.noarch.rpm http://192.168.17.20/repo/pub/openeuler/oe2003/smartx-iso-common/aarch64/Packages/python-zope.interface-5.4.0-1.aarch64.rpm http://192.168.17.20/repo/pub/openeuler/oe2003/smartx-iso-common/aarch64/Packages/python-leveldb-0.201-1.aarch64.rpm

RUN dnf install -y http://192.168.17.20/repo/pub/openeuler/oe2003/rc/aarch64/zbs-client-py-1.0.8-rc1.0.oe1.aarch64.rpm
```
