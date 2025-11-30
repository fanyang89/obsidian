#ZooKeeper

```java
// 假设session id是一个64位的long型变量
long sessionId = 0x26d9d24aec60008L;

// 使用位运算符和移位操作来提取不同的字段
int sid = (int) (sessionId >>> 56); //右移56位，得到最高8位，即server id
long timestamp = (sessionId << 8) >>> 16; //左移8位，再右移16位，得到中间40位，即timestamp
int counter = (int) (sessionId & 0xFFFF); //与0xFFFF做按位与，得到最低16位，即counter

// 打印结果
System.out.println("sid: " + sid);
System.out.println("timestamp: " + timestamp);
System.out.println("counter: " + counter);
```
