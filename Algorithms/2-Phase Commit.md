
2-Phase commit is a widely used algorithm for replicating data in a cluster of servers. It is a distributed algorithm that ensures a transaction either completes(commit) or abort(rollback) in all the nodes of the cluster.

![](https://miro.medium.com/v2/resize:fit:320/0*FBQPiHKMRmrPE_mc.png)


####  1. Prepare Phase
---
- The coordinator node which receives the request initiate a transaction and sends prepares messages to all participant nodes.
- Each participant executes the transaction locally and stores the result in its local log.
- The results are then send to the coordinating server, 
- If the participant **executes successfully** it returns ***commit*** to the coordinating server.
- If some **error occurs**, it returns ***abort/rollback*** to the coordinating server.

#### 2. Commit/Abort Phase
---
- If all participants vote to commit, the coordinator decides to commit the transaction. Otherwise, the transaction is aborted.
- The coordinator sends a "commit" or "abort" message to all participants.
- The transaction is considered complete when the coordinator receives acknowledgments from all participants.
- Acknowledgements is sent by the participant server after it commits the transaction.