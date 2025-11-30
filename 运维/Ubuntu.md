#Ubuntu

## 设置镜像源

```bash
sed -i 's/http:\/\/cn.archive.ubuntu.com/http:\/\/mirror.sjtu.edu.cn/g' /etc/apt/sources.list
sed -i 's/http:\/\/security.ubuntu.com/http:\/\/mirror.sjtu.edu.cn/g' /etc/apt/sources.list
```
