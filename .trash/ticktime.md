## TickTime
The basic time unit in milliseconds used by ZooKeeper. It is used to do heartbeats and the minimum session timeout will be twice the tickTime.

ZooKeeper 的基本时间单位。SessionTimeout 最小 $ [2 * tick, 20 * tick] $

默认的 tickTime 是 2000ms，所以 

如果要修改 tickTime=1000ms。
