```
Fixes ZBS-11961 lsm1: fix may read old data

Current LSM1 may read old data. The sequences is:
  1. begin write
  2. begin read
  3. ssd unplug
  4. write journal fails, so write fails
  5. because Cache OFFLINE, set extent to INVALID
  6. read no need journal, and read block successes

Even the write and read overlaps, this read still return OK. This
patch fixes it.

Change-Id: I6669b0261871c1185baf9e121fd83781fae58b91
```

