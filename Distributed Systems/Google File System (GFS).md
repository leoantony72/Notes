
---
**GFS** fixes the bad replication design shown below, The below design causes **inconsistency issue** when multiple clients wants to update the same key at the same time.

![[Distributed Systems/distributed_system.excalidraw.md#^group=w7yhjii_29JvVUmrq7hWx|400]]

### Architecture
---
- Files are broken down to chunks. each chunk is 64mb and will have a unique ID called namespace.
- client asks for the file to Master
- Master server returns the address of the server where the file is stored.
- Master  server also stores he metadata of each file
- Master on startup will approach the chunk server and get which files they hold.

![](https://miro.medium.com/v2/resize:fit:640/format:webp/1*WI0yXoUvjLmHDkT_UJLVuw.jpeg)

- Each chunk is stored on multiple chunk servers.
- When a chunk needs to be updated, the client contacts the master server to obtain the list of chunk servers that hold replicas of the chunk.
- Writes always take place on the **primary** chunk server.
- The **master server grants a lease** to one of the chunk servers, designating it as the primary for a specific chunk.

### Master Server
---
- stores the list of chunk server in Disk, each chunk has a version number.
- Logs and Checkpoints are stored in Disk.
- Metadata of the files/chunk are stored in the Master server.
- Grants lease for the chunk to the chunk server(becomes primary).

### Chunk Server
---
- Stores the files in chunks.
- All the read request is served by the chunk server
- write requests are served by the primary chunk server assigned by the master server for each chunk.
- Once a lease is granted, the chunk server must periodically **renew** the lease.

#### Reading a Chunk
---
1. Send filename and offset to Master.
2. Master sends chunk handle & list of server
3. client talks to one of the chunk server

#### Writing to a Chunk
---
1. If there is no primary, Master chooses the chunk server which will handle the write.
	1. Find up-to date Replica, to find the chunk server with version number equal to that stored in Master server .
	2. Picks one of the server to be the new primary and others to be secondary server. Master grants the primary the lease.
2. Master increments the version number to disk
3. Tells the primary and secondary to increment the version number
4. The client **streams the data** to the primary chunk server, which stores the chunk and then forwards the data to the secondary servers (the replicas).
5. The chunk servers accept the data in a **pipelined fashion**, meaning:
    - The primary chunk server writes the chunk to its local disk.
    - The primary server then **streams the chunk data** to the second replica, which stores the chunk, and finally, to the third replica.
	- This **pipelining** ensures that data is written in parallel to all replicas without waiting for one server to finish before moving to the next.


#### During a Master reboot
---
- When the chunk server has a higher version than the master during reboot, the master checks the chunk’s data and version, and resolves the conflict by either accepting the chunk server's version or ensuring the data is consistent across all replicas.
- If the chunk server's data is inconsistent, the master may ask the chunk server to correct or discard the data.
#### Real world use
1. GFS is completely stored in a single Data Center, not optimal for global distribution.
2. Big Sequential access.


### Conclusion
---
- Single point of failure, If the Master goes down entire system fails.
- Poor handling of Small Files, GFS introduces a significant overhead, as each file is stored as a chunk with its own metadata, and many small files can overwhelm the master node. only a small part of a chunk is used by a small file, the rest of the chunk remains wasted.
- GFS does not support atomic updates for multiple chunks.
- Optimized for large files