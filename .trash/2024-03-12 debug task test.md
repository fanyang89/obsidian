```
[  PASSED  ] 10 tests.
[  FAILED  ] 5 tests, listed below:
[  FAILED  ] TaskdTest.ReschedulerUnfinished
[  FAILED  ] TaskdTest.ReschedulerTaskWhenLeaderAndRunnerFailed
[  FAILED  ] TaskdTest.ReschedulePendingTask
[  FAILED  ] TaskdTest.ConfigVIP
[  FAILED  ] TaskdTest.DynamicPriority
```

```
#4  0x000055555d0e1ac5 in zbs::CoMutex::Lock (this=0x6190000b1e78) at ../src/common/co_locks.cc:21
```

