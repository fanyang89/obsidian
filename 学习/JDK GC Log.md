#java

```bash
java -jar ./gclog-1.0-SNAPSHOT.jar -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=10K -XX:+PrintGCDetails \
-XX:+PrintGCDateStamps -Xloggc:/home/fanmi/tmp/gclog/gc-%t.log \
-XX:+UseG1GC -XX:MaxGCPauseMillis=200
```
