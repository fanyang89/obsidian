#cpp #zbs #sanitizer
忽略 protobuf 初始化产生的 race，他自己实现了 once init，tsan 支持需要 protobuf 3

## RACE#1

Quorum T#5 Write
![](assets/Pasted%20image%2020230616143142.png)

rpc-server T#2 read
![](assets/Pasted%20image%2020230616143240.png)

system-manager T#1 Alloc
![](assets/Pasted%20image%2020230616143418.png)
