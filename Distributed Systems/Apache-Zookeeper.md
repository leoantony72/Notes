
Zookeeper has a similar architecture as RAFT, 
- It keeps a log of commands it receives
- Has a leader node
- Follows **[[2-Phase Commit]]**
- ZAB is the equivalent protocol to RAFT

![[Distributed Systems/distributed_system.excalidraw.md#^group=tEv71YHDKsWW9gpLBpRkw|600]]

**In Zookeeper write takes place in the leader node and is replicated to the follower nodes.**
**This may lead to consistency issue as a follower may not be in Sync/Up-to date with the Leader.**

Every request are first stored in the ***ZAB protocol Log***, only after the replication phase the data is written to the database. Here [[2-Phase Commit]] is used to replicate the data onto other followers.