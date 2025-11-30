#raft #consensus

## 摘要

- However, auto scaling approaches for Raft inflate costs by provisioning at all sites when one site exhausts its local resources.
- Geo-Raft extends Raft with the following abstractions:
  1.  secretaries which takes log processing for the leader
  2.  observers which process read requests for followers

## 导论

- Geo-Raft supports 4 types of software threads
  - followers
  - leaders
  - secretaries
  - observers
- In Geo-Raft, leaders outsource a portion of their workload to secretaries, reducing workload imbalance.
- Secretaries and observers are stateless and execute at any geo-distributed site.

## 设计

Secretaries and observers can run on cheap, failure-prone resources, making them well suited for geographically distributed spot markets.
