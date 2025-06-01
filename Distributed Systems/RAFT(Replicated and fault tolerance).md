
---
Raft is a consensus algorithm, Consensus means multiple servers agreeing on same information.
It has 3 states:
1. Leader
2. Candidate
3. Follower

![[Distributed Systems/distributed_system.excalidraw.md#^group=pHmBmSP3Wih-_sGEK2sIs|700]]

When a client needs to insert a key,
1. Client makes a request to insert a key to the leader node. The leader appends this request as a new **log entry** in its log.
2. The leader then starts replicating this log entry to its **followers** using the `AppendEntries` RPC. Each follower appends the entry to its own log and sends an acknowledgment to the leader.
3. The leader waits for a **majority of followers** to acknowledge that they’ve successfully appended the log entry. This acknowledgment means that the log entry is safely replicated to a majority of nodes, ensuring that it won’t be lost even if some followers crash or become unavailable.
4. Once the leader has received acknowledgment from a majority of followers, it considers the log entry as **committed**. The entry is now guaranteed to be part of the system’s state.
5. Leader Sends the Final Confirmation to the Client, After the leader receives acknowledgment from the majority of followers (i.e., the entry is committed), the **leader sends a final response to the client**, informing the client that the request has been successfully processed.

### Leader Election in RAFT
---
Leader server sends **AppendEntries()** to all of its followers. If there are no data to commit then empty log is send. Receiving of the **AppendEntries()** is treated as a Heartbeat.

If the follower server does not get heartbeats for their specified '**Request Timeout**' time then the follower becomes the candidate. Each server will have different Request timeout time to prevent multiple followers becoming the candidate at the same time.

When a server becomes a candidate the server sends **RequestVote()** RPC call to all the followers.

On receiving a majority votes the candidate becomes the leader.

<mark style="background: #FFB86CA6;">Due to latency a follower maybe converted to a candidate:</mark>
- the server rejects AppendEntrie() request if the candidate term is larger or equal to the already existing leader.
- then the candidate sends its term along with failed message. This failed message marks the old leader to step down into follower state.

<mark style="background: #FFB86CA6;">Suppose when a leader receives a write command, it writes to its log and then fail due to any issues</mark>
- we now have a server which has additional data that hasn't been replicated on the follower servers.
- When the server restarts after the new election has ended. it will synchronize with the current leader. (the leader will only append the data, no deleting will happen)
- the non-committed data is deleted from the server.
- if the server reboots before the election, the server has the longest log index and upon receiving requestVote() rpc it rejects the req(due to less log index in the requesting server). Thus eventually the server with the highest log index will becomes the new leader.


## Conclusion

- Every data is replicated in every server, Therefore not ideal for large data systems.
- Leader node handles all the writes.
- Leader append the entry to log and sends to its follower via **AppendEntrie()** rpc.
- Once majority of the followers acknowledge the new entry, the leader marks it as **commited**.
- Then all the followers apply the log to the database(state machine).

- When a leader fails, one of the followers becomes the candidate and holds the election.
- The followers know the leader has failed when the leader stops sending **AppendEntrie() rpc**.
- The candidate server sends **RequestVote() rpc** to all the followers and on receiving the majority it becomes the leader.