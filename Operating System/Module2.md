#### Syllabus
---------
1. [[#1.Process, Process State|Processes, Process states]]
2. [[#2.Process Control Block (PCB)|Process Control Block, Threads]]
3. Scheduling
4. Operations on processes: process creation and termination
5. Inter-process communication: Shared memory systems, Message Passing
6. Process Scheduling – Basic concepts, Scheduling Criteria
7. Scheduling algorithms - Basics
8. First come First Served, Shortest Job First
9. Priority scheduling, Round Robin Scheduling

## 1.Process, Process State
---------
![[multiple-process.png]]
When a program is executed, It becomes a process. A process is a program in execution. It is an active entity and dynamically changing. An OS considers processes as units of program execution or simply computation. A process contains application program (sequence of instructions) and data (arguments, variables etc...). A single program can have multiple processes running at the same time on a given machine even if the system has only 1 core.

Some of the processes can belong to application programs or user programs (called user processes), and some OS programs (called kernel processes).

Each process holds some attributes given by the OS as follows.

**Process-id**: a process identifier (pid)
**User-id**: the process is owned by a specific user (uid)
**Process Group-id**: Every process is supposed to belong to a group, based on the task. The group has a process group identifier (or pgid) 
**Address space**: main memory space (known as process address space) where it stores
   1. program (code)
   2. static data 
   3. dynamic data in the form of **heap** and **stack**

Kernel processes reside in kernel space (of main memory), execute OS kernel code in kernel mode, while user processes remain in user space and run user code in user mode and can make system calls 
![[Different_parts_of_process_addressspace.png]]

### Process address space
When a program is executed, It is allocated space in the main memory. The space is called process address space. The space may be physically contiguous or non-contiguous depending on the memory allocation technique used by the OS.
- Text Section: It stores the program code (in executable form, not the source code).
- Data Section: This section stores the data that are used by the process. Some data comes attached to the program code that cannot be dynamically changed (globally declared and initialized as read-only data) - we call this as program data
    - Static data: This data is statically bound to program code and can be allocated space during compilation.
    -  Dynamic data: This data is not allocated during compilation, rather can only be allocated during program execution or in the run-time. It can grow or shrink during execution depending on the requirement of the process. It has two important sub-sections. 
         - a. Heap: During program execution the process dynamically allocates memory (as done by malloc()in Fig. 2.2) based on requirement and deallocates when the need is over.
         - b. Stack: This space is used by the arguments, local variables, return values of a function or a method within a source program. For each function call, stack stores the above variables and data structures for it. 
### Process State
![[process_state.png]]
1. New: This is the first state of a process. When a process is created or a program is invoked the OS creates a new execution context, allocates a process address space in the main memory and other necessary per-process resources in the kernel mode. 

2. Ready: Once the per-process resources are created, the process becomes ready for execution. It needs a processor (actually, a core of a processor, to be specific) to be allocated. 

3. Running: As soon as a processor is allocated, the process starts executing the instructions from the program text. Here, the program can run in user mode, However, for privileged instructions, it can go to kernel mode also. 

4. Waiting: When the process needs an I/O to be done or explicitly waits (via a system call like wait()), the process is taken off the processor and is considered to be waiting. When the I/O is complete or the explicit wait is over, the process becomes ready and joins the ready queue. It can run only when it is allocated to the processor. 

5. Terminated: When the process completes normally (even abnormally also), process address space is reclaimed by the OS. All process-related resources are also de-allocated. This state is called a terminated state.

### Process Context
After a process is created, it changes its states from one to another as it proceeds in its life cycle. Along with the states, there are several other parameters of a live process that the OS kernel has to keep track of. For example, which instruction a process is currently running and thus where the CPU will find the next instruction from (remember program counter or PC), how many special purpose registers (SPRs) like stack pointer (SP), program status word (PSW), condition codes (CC) it is using and what are their values.

## 2.Process Control Block (PCB)
The OS kernel maintains a special type of data-structure called PCB in it's kernel space for each live process. This data-structure stores the context of a process.
- Process id
- User id
- Process state: process state can be new, ready, running, waiting, terminated
- Scheduling information: for CPU allocation to process, process priority, pointers to scheduling queues, and other scheduling parameters need to be maintained.
- Memory-management information
- Accounting information: Information like amount of CPU time used, wait time, time limits etc.
- Software context: This can be a list of open files, open sockets (ip-address + port-address, used for communicating with remote processes) and memory regions
- Hardware context: There are a number of hardware information that need to be kept track of like Program Counter, stack pointer, cpu registers, I/O devices.
- Pointers to different data structures
### Process Table
![[processTable.png]]
In a multiprogramming OS, multiple processes concurrently run. The kernel thus has to maintain more than one PCB. Often the PCBs are stored as a list in a table. This is a kernel data structure called a Process Table.