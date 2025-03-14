#### Syllabus
----
1. [Process Synchronization](#1.Process-Synchronization)
2. [Race Conditions](#2.Race-Conditions)
3. [Critical Section Problem](#3.Critical-Section-Problem)
4. [Mutex Locks](#4.Mutex-Locks)
5. [Peterson’s Solution](#5.Petersons-Solution)
6. [Synchronization Hardware](#6.Synchronization-Hardware)
7. [Semaphores](#7.Semaphores)
8. [Monitors](#8.Monitors)
9. [Synchronization Problems](#9.Synchronization-Problems)
10. [Producer-Consumer Problem](#10.Producer-Consumer-Problem)
11. [Dining Philosophers Problem](#11.Dining-Philosophers-Problem)
12. [Readers-Writers Problem](#12.Readers-Writers-Problem)
13. [Deadlocks](#13.Deadlocks)
14. [Resource Allocation Graphs](#14.Resource-Allocation-Graphs)
15. [Deadlock Prevention](#15.Deadlock-Prevention)
16. [Deadlock Avoidance – Banker’s Algorithm](#16.Deadlock-Avoidance-%E2%80%93-Banker%E2%80%99s-Algorithm)
17. [Deadlock Detection](#17.Deadlock-Detection)
18. [Recovery from Deadlock](#18.Recovery-from-Deadlock)


## 1.Process-Synchronization
---
All the cooperating processes either share data through them or modify them. It may happen that more than one process attempts to simultaneously access the same data-structure (or shared variables) at the same time. Simultaneous read of a given shared data by several processes may lead to a contention in a single processor system as to who gets the first chance to read. Even though this is a scheduling issue, it is not a serious problem. All processes are supposed to read the same value of the variable or face the same state of the shared data structure. But when simultaneous read and write attempts are made or simultaneous writes are attempted on a shared data item by more than one process - their execution order has serious implications.

But coordination or synchronization among the cooperating processes is necessary to ensure that simultaneous reads & writes are properly serialized to mitigate undesirable program behaviour. It is one of the most important issues in concurrent programming (refer Sec 1.2.3 and Sec 1.3.8) that needs to be taken care of either by the application developers or the operating system.

In a non-preemptive uniprocessor system, a process is allowed to execute as long as it wants until it voluntarily leaves the processor. Only one process gets the chance to use CPU and complete the read or write operations at a given time unhindered. Hence, this kind of erroneous sequence is not supposed to occur unless there is a mistake within the process committed by the programmer. In a non-preemptive multiprocessor system, however, speeds of the processors also need to be taken into account. Scheduling cooperating processes on different processors with different computing speeds can create serious synchronization issues.

The problem can aggravate in a preemptive scheduling system because a process can be preempted while it is in the middle of a write operation. The write may not be complete before the preemption and another cooperating process may read the same data unaware of the preemption. If the read is supposed to happen after the write, but the write is not complete, and the reader is not aware of it - this leads to the problem of data inconsistency. The reader will get wrong data which can lead to further undesirable effects.

## 2.Race-Conditions
---
Race conditions happens when two or more process write at the same time, Then it is a case of simultaneous writes. This type of simultaneous write can lead to multiple possible values if it happens in parallel or it will be one or the other values that can be possible in simultaneous writes.

## 3.Critical-Section-Problem
---
 the update needs to be indivisible or atomic, i.e., if it has started, it must be complete, or it should be rolled back and should start afresh. The problem occurred as preemption was allowed within an update of a shared variable. Operating systems must ensure that shared variables are updated in an atomic manner - preemption should not happen while shared data is being modified. The section of code where a shared data is accessed is thus very critical - the section is called a critical section (CS). For example, where a database file is updated is a critical section. 
 
When a shared variable is accessed in a process, the first instruction with which the access begins marks the start of a CS and the instruction where manipulation is complete marks the end of a CS. For a given shared variable, there can be several CS for it within a process. Also, for the same shared variable, there can be different CS in different processes. The length of a CS can vary depending on the type of data structure and its manipulation type. A process can have several critical sections related to a single or multiple shared objects.


Let us consider a system of n processesܲ  P0,P1,P2,....P(n-1) sharing at least a shared data item among them. Each of the processes has a critical section where the process accesses or modifies data that is shared with at least another process. As already discussed, no two processes should be allowed to access the CS at the same time. Any process that wants to access a CS must make a request to enter the CS. If there is no other process executing the CS, the requesting process will be allowed to enter the CS, otherwise it needs to wait. The section of code where the request is made, and the process is granted permission or needs to wait is called an entry section. Every CS will be preceded by an entry section. Similarly, when the execution of a CS is done by a process, some bookkeeping jobs need to be done, so that other processes waiting for permission to enter the CS, can enter in their CS. This portion of code where book-keeping work is done just after a CS is called an exit section. Every CS will be followed by an exit section. Other portions of the code in each of the processes are called the remainder section.

Any good solution to the critical section problem must have three following properties:
- Mutual Exclusion: One  and only one process is allowed to execute in a critical section corresponding to a shared data object at any time. In other words, access to the CS is done mutually exclusively. This is also known as the safety property
- Progress: If no process is executing in a CS, but some other process(es) want(s) to enter the CS, then the processes which are not in the remainder section (that means processes in either entry or exit or critical sections) will decide which process can enter in the CS next. Also, this decision must be taken within a bounded time. This is also known as finite arbitration or liveness property.
- Bounded Wait: Once a process has made a request to enter a critical section, there must be a limit or bound on how many times other processes can be allowed to enter the CS before the requesting process is granted access to enter the CS. This is also called the property of starvation freedom

## 4.Mutex-Locks
---
Each access to a critical section is to be ensured in a protected manner, ***Only one process should be allowed at a time to access a critical section shared by many processes***.  If some shared data is updated, then it should be done in an atomic way (either update should be complete or not done at all). While the update is in progress, no other processes should interfere but wait till it is complete. In other words, access to critical sections is to be done mutually exclusively.

Mutex is the acronym of mutual exclusion. A mutex lock is a simple synchronization tool designed to ensure mutual exclusion of two or more processes sharing a critical section. It can be thought of as a simple data structure containing a boolean variable, say avail. The var avail is initialised as true. A mutex lock allows two simple operations: acquire() and release(). The mut_lock is acquired (only if avail is true) by a process before going into a CS, and released (avail is set to true) when the execution in the CS is done (Fig 3.19). Operation acquire() resets the variable avail (=false) so that other processes that want to execute CS loops around in the busy wait condition. However, if a process that holds the lock releases it, another process can acquire and go into the CS.

- The two operations acquire() and release() are considered atomic. They can be implemented using hardware primitives like TSL or CAS. 
- This type of mutex locks has a busy-wait loop where a process intending to go into CS loops around. These are also, therefore, called spinlocks. Spinlocks are not good from the performance point of view. Nonetheless, spinlocks are beneficial when critical sections are very small needing mutual exclusion for only short duration.

## 5.Petersons-Solution
----
Two flag variables are introduced in the peterson's solution to 2-process CSP(this only works for only 2 concurrent process).  It allows **two processes** to share a **critical section** without leading to **race conditions**. It ensures **mutual exclusion, progress, and bounded waiting**—the three necessary conditions for a synchronization algorithm.

We use two **variables**:
1. `flag[2]` → A boolean array where `flag[i] = true` means process **Pi** wants to enter the critical section.
2. `turn` → An integer variable indicating whose turn it is to enter the **critical section**.
```go
// Peterson's Solution in Golang
// Process P0
do {
    flag[0] = true;   // P0 wants to enter critical section
    turn = 1;         // Give turn to P1

    while (flag[1] && turn == 1);  // Wait if P1 wants to enter

    // Critical Section
    // Perform shared resource operations

    flag[0] = false;  // Exit critical section
} while (true);

// Process P1
do {
    flag[1] = true;   // P1 wants to enter critical section
    turn = 0;         // Give turn to P0

    while (flag[0] && turn == 0);  // Wait if P0 wants to enter

    // Critical Section
    // Perform shared resource operations

    flag[1] = false;  // Exit critical section
} while (true);

```

- **Mutual Exclusion:** Only one process can enter the **critical section** because the other process is forced to wait.

- **Progress:** If one process is not in the **critical section**, the other can proceed.

- **Bounded Waiting:** The `turn` variable ensures that neither process gets **stuck forever** waiting.


## 6.Synchronization-Hardware
---
