# 💻 Operating Systems — Complete Master Notes

> **Purpose:** This is a complete college + placement preparation resource for Operating Systems.
> - Use this as your **primary study material** to understand OS deeply.
> - Use your college PPT for **quick revision** and professor-specific terminology.
> - Sections marked *College Foundation* map to Silberschatz/Galvin (your PPT source).
> - Sections marked *Placement Extension* go beyond current college coverage for interviews.

---

## OS Roadmap — How Topics Connect

```
OS Fundamentals (What is OS, Kernel, Goals)
        ↓
System Calls & OS Interface (User ↔ Kernel boundary)
        ↓
Interrupts & Exceptions (Hardware signals to CPU)
        ↓
I/O Systems (Devices, Drivers, DMA)
        ↓
Process Management (Program → Process, PCB, States)
        ↓
Threads (Lightweight processes, Multithreading)
        ↓
CPU Scheduling (How CPU picks next process)
        ↓
Process Synchronization (Race conditions, Mutex, Semaphore)
        ↓
Deadlocks (Coffman conditions, Prevention, Avoidance)
        ↓
Memory Management (Paging, Segmentation, Fragmentation)
        ↓
Virtual Memory (Demand paging, TLB, Page replacement)
        ↓
File Systems (Files, Directories, Allocation)
        ↓
Mass Storage & Disk Scheduling
        ↓
Caching & Storage Hierarchy
        ↓
Protection & Security
        ↓
OS Types & Structures
```

---

## Table of Contents

1. [OS Fundamentals](#1-os-fundamentals)
2. [System Calls & OS Interface](#2-system-calls--os-interface)
3. [Interrupts & Exceptions](#3-interrupts--exceptions)
4. [I/O Systems](#4-io-systems)
5. [Process Management](#5-process-management)
6. [Threads](#6-threads)
7. [CPU Scheduling](#7-cpu-scheduling)
8. [Process Synchronization](#8-process-synchronization)
9. [Deadlocks](#9-deadlocks)
10. [Memory Management](#10-memory-management)
11. [Virtual Memory](#11-virtual-memory)
12. [File Systems](#12-file-systems)
13. [Mass Storage & Disk Scheduling](#13-mass-storage--disk-scheduling)
14. [Caching & Storage Hierarchy](#14-caching--storage-hierarchy)
15. [Protection & Security](#15-protection--security)
16. [Types of Operating Systems](#16-types-of-operating-systems)
17. [OS Structures & Architectures](#17-os-structures--architectures)
18. [Important OS Concept Connections](#18-important-os-concept-connections)
19. [High-Yield Comparisons](#19-high-yield-comparisons)
20. [Common Interview Questions](#20-common-interview-questions)
21. [Interview "Why" Questions](#21-interview-why-questions)
22. [Common Confusions & Mistakes](#22-common-confusions--mistakes)
23. [Quick Revision Sheet](#23-quick-revision-sheet)
24. [OS Interview Checklist](#24-os-interview-checklist)

---

## 1. OS Fundamentals

*College Foundation — Silberschatz Ch. 1*

### What is an Operating System?

**Simple Meaning:** The OS is the software that sits between your programs and the hardware. Without it, every application would have to talk to hardware directly — which is impractical and unsafe.

**Technical Definition:** An operating system is a program that acts as an intermediary between users and computer hardware. It manages hardware resources and provides an environment in which programs can run conveniently and efficiently.

**The four-layer view:**
```
+---------------------------+
|         Users             |
+---------------------------+
|    Application Programs   |  (browsers, editors, games)
+---------------------------+
|    Operating System       |  ← manages everything below
+---------------------------+
|       Hardware            |  (CPU, RAM, Disk, I/O)
+---------------------------+
```

### Goals of an OS

1. **Execute user programs** — make solving user problems easier.
2. **Make the computer system convenient to use** — abstract away hardware details.
3. **Use hardware in an efficient manner** — resource utilization.

### OS as Resource Allocator

The OS manages all resources: CPU time, memory space, I/O devices, files.
When multiple programs compete for the same resource, the OS decides **who gets what, when, and for how long**.

Think of the OS as a **government** — it doesn't do useful work itself, but it manages shared resources and enforces rules so that everything runs fairly.

### OS as Control Program

The OS controls the execution of programs to prevent errors and improper use of the computer. It ensures that no program monopolizes the CPU, corrupts another program's memory, or directly accesses hardware unsafely.

### User View vs System View

| Perspective | Goal |
|-------------|------|
| **User View** | Ease of use, good performance, don't care about resource sharing |
| **System View** | Resource allocation, control, efficiency, fairness |

### Computer System Components

1. **Hardware** — CPU, memory, I/O devices; provides basic computing resources.
2. **Operating System** — controls and coordinates hardware among applications.
3. **Application Programs** — define how resources are used to solve user problems.
4. **Users** — people, machines, or other computers.

### Kernel vs Operating System

| | OS | Kernel |
|--|----|----|
| **Scope** | Entire software managing hardware | Core part of the OS that runs at all times |
| **Size** | Larger — includes utilities, shell, libraries | Smallest essential part |
| **Role** | Broad resource management | Direct hardware interaction |

> **Key point:** The kernel is always running in memory. Everything else in the OS is loaded when needed.

**OS = Kernel + System Programs + Utilities**

### Bootstrap / Boot Process

When you power on a computer:
1. CPU runs the **bootstrap program** (stored in ROM/firmware, e.g., BIOS/UEFI).
2. Bootstrap initializes hardware (CPU registers, device controllers, memory).
3. Bootstrap locates and loads the OS kernel into memory.
4. Kernel starts executing — initializes system, mounts file systems, starts services.
5. OS presents login prompt or GUI.

> The bootstrap program (BIOS/UEFI) is the very first software that runs. It hands control to the OS kernel.

**Priority:** ⭐⭐⭐⭐

---

## 2. System Calls & OS Interface

*College Foundation — Silberschatz Ch. 2*

### What is a System Call? ⭐⭐⭐⭐⭐

**Simple Meaning:** When a program needs something from the OS (like reading a file or allocating memory), it cannot do it directly. Instead it makes a **system call** — a request to the OS kernel.

**Technical Definition:** A system call is a programmatic way for a user-level program to request a service from the operating system kernel. It provides a controlled entry point into the kernel.

### Why System Calls Are Needed

User programs run in **user mode** with restricted access. They cannot directly touch hardware (disk, network, etc.) because:
- It would be unsafe — any program could corrupt any memory.
- Hardware has complex interfaces — the OS abstracts them.
- Resource sharing requires coordination — the OS manages it.

So user programs request services via system calls, and the OS performs those privileged operations on their behalf.

### API vs System Call

| | API | System Call |
|--|-----|-------------|
| **What** | Function in a library that you call | Actual request to the OS kernel |
| **Who uses it** | Programmers | OS internally |
| **Example** | `fopen()` in C, `open()` in Python | `open` syscall (Linux syscall #2) |
| **Level** | User level | Kernel level |

> `printf()` → calls `write()` API → which invokes the `write` system call → kernel writes to terminal.

Most programmers interact with the **API** (e.g., POSIX, Win32), not system calls directly. The API hides the low-level details.

### User Mode and Kernel Mode ⭐⭐⭐⭐⭐

**Mode Bit:** A single bit in the CPU's status register that tells whether the CPU is in user mode (1) or kernel mode (0).

| | User Mode | Kernel Mode |
|--|-----------|-------------|
| **Access** | Restricted — cannot execute privileged instructions | Full — can execute any instruction |
| **Who runs in it** | User programs, applications | OS kernel |
| **Also called** | Problem mode | Monitor mode / Supervisor mode / System mode |
| **Hardware access** | Not allowed directly | Allowed |

**Privileged Instructions:** Instructions that can only execute in kernel mode.
Examples: halt the CPU, control I/O devices, manage memory, enable/disable interrupts.

If a user program tries to execute a privileged instruction → hardware trap → OS terminates the program.

### System Call Flow

```
User Program
    |
    | calls open("file.txt")  ← API call
    ↓
C Library (glibc / Win32)
    |
    | invokes system call (trap instruction)
    ↓
MODE SWITCH: User Mode → Kernel Mode
    |
    ↓
Kernel (OS)
    |
    | performs the operation (accesses disk, etc.)
    | returns result
    ↓
MODE SWITCH: Kernel Mode → User Mode
    |
    ↓
User Program resumes with result
```

The **trap instruction** (also called software interrupt) switches CPU to kernel mode and jumps to the OS kernel's system call handler.

### Categories of System Calls

| Category | Examples |
|----------|---------|
| **Process control** | `fork()`, `exec()`, `exit()`, `wait()` |
| **File management** | `open()`, `read()`, `write()`, `close()`, `delete()` |
| **Device management** | `ioctl()`, `read()`, `write()` (device) |
| **Information maintenance** | `getpid()`, `alarm()`, `time()` |
| **Communication** | `pipe()`, `socket()`, `send()`, `recv()` |
| **Protection** | `chmod()`, `umask()`, `chown()` |

**Interview Angle:** "What happens when you call `read()` in C?" — Walk through the API → system call → mode switch → kernel reads → returns to user mode chain.

**Priority:** ⭐⭐⭐⭐⭐

---

## 3. Interrupts & Exceptions

*College Foundation — Silberschatz Ch. 1*

### What is an Interrupt? ⭐⭐⭐⭐⭐

**Simple Meaning:** An interrupt is a signal from a device or software to the CPU saying "stop what you are doing for a moment — something needs your attention."

**Technical Definition:** An interrupt is a hardware or software signal that causes the CPU to suspend execution of the current program, save its state, and transfer control to a special routine called the **Interrupt Service Routine (ISR)**.

### Why Interrupts Are Necessary

Without interrupts, the CPU would have to constantly *poll* (repeatedly check) every device to see if it needs attention — wasting CPU cycles. Instead, devices *interrupt* the CPU only when they actually need service.

**Analogy:** Instead of repeatedly asking "are you done yet?" (polling), the device taps you on the shoulder when it's done (interrupt).

### Hardware Interrupt

Triggered by an **I/O device** (e.g., keyboard press, disk read complete, network packet arrived).

Flow:
```
Device completes operation
    ↓
Device Controller sends interrupt signal to CPU
    ↓
CPU finishes current instruction
    ↓
CPU saves state (registers, Program Counter)
    ↓
CPU looks up Interrupt Vector → finds ISR address
    ↓
CPU jumps to ISR (Interrupt Service Routine)
    ↓
ISR handles the event (e.g., reads data from controller)
    ↓
CPU restores saved state, resumes interrupted program
```

### Interrupt Vector

A table (stored in low memory or a special register) that maps each interrupt number to the address of its ISR. The CPU uses this table to quickly find the correct handler for each interrupt type.

### Software Interrupt / Trap

A **trap** (or software interrupt) is an interrupt generated by software — either:
- By a **system call** (intentional — program requesting OS service), or
- By an **error/exception** (unintentional — divide by zero, invalid memory access).

| | Hardware Interrupt | Software Interrupt (Trap) |
|--|-------------------|--------------------------|
| **Source** | External device | Software (system call or exception) |
| **Trigger** | Device signals CPU | Trap instruction or error |
| **Intentional?** | Usually yes (I/O complete) | System calls: yes; Exceptions: no |
| **Example** | Keyboard press | `read()` syscall, divide-by-zero |

### Saving CPU State

When an interrupt occurs, the CPU must save its current state so it can resume later:
- **Program Counter (PC)** — address of the next instruction
- **CPU Registers** — all register contents
- **Status flags** — condition codes

This saved state is typically pushed onto a **kernel stack**.

### Polling vs Interrupt

| | Polling | Interrupt |
|--|---------|-----------|
| **Mechanism** | CPU repeatedly checks device status | Device signals CPU when ready |
| **CPU usage** | Wastes CPU cycles checking | CPU free to do other work |
| **Latency** | Depends on polling frequency | Low — immediate response |
| **Use case** | High-speed devices, simple embedded | Most general-purpose I/O |

**Priority:** ⭐⭐⭐⭐

---

## 4. I/O Systems

*College Foundation — Silberschatz Ch. 1*

### The I/O Problem

Applications need to use I/O devices (disk, keyboard, network). But:
- Each device is physically different and has its own interface.
- I/O is slow compared to CPU.
- Multiple processes may need the same device simultaneously.

The OS solves this by **hiding hardware-specific details** behind a uniform interface and managing concurrent access.

### Device Controller

A **device controller** is a hardware component (chip/circuit) that:
- Sits between the CPU and the actual I/O device.
- Has its own **local buffer** (small memory) and **registers**.
- Controls the device's electrical signals.
- Signals the CPU via **interrupt** when an operation is complete.

**Example:** A disk controller sits between the CPU and the hard disk. The CPU tells the controller "read sector 100," the controller does it, and signals the CPU when done.

### Device Driver

A **device driver** is **software** (part of the OS) that:
- Knows how to communicate with a specific device controller.
- Translates OS-level I/O requests into controller-specific commands.
- Is usually written by device/hardware manufacturers.

```
Application
    ↓ system call
Kernel I/O Subsystem
    ↓
Device Driver  ← software (OS layer)
    ↓
Device Controller ← hardware
    ↓
I/O Device (disk, keyboard, etc.)
```

| | Device Driver | Device Controller |
|--|--------------|-------------------|
| **Type** | Software | Hardware |
| **Location** | OS kernel | Inside device / on motherboard |
| **Role** | Translates commands | Executes commands electrically |

### Buffering, Caching, Spooling

| Technique | Purpose | Example |
|-----------|---------|---------|
| **Buffering** | Temporarily store data while transferring between two devices with speed mismatch | Network data buffered in RAM before processing |
| **Caching** | Copy of frequently-used data in faster storage for quick access | Disk blocks cached in RAM |
| **Spooling** | Queue jobs for a device that cannot handle multiple simultaneous requests | Printer spool queue |

### Interrupt-Driven I/O

1. CPU sends I/O command to device controller.
2. CPU **does other work** (doesn't wait).
3. Controller performs I/O operation.
4. Controller interrupts CPU when done.
5. CPU handles interrupt, processes data.

This is efficient — CPU is not idle while waiting for I/O.

### Direct Memory Access (DMA) ⭐⭐⭐⭐

**Problem with non-DMA I/O:**
```
Without DMA:
Device → CPU → Memory
CPU must copy each byte from device buffer to memory — wastes CPU cycles.
```

**DMA Solution:**
```
With DMA:
Device → DMA Controller → Memory (directly)
CPU only sets up the transfer and gets interrupted when complete.
```

**How DMA works:**
1. CPU programs the **DMA controller** with: source address, destination address, size.
2. DMA controller takes control of the memory bus.
3. Data moves directly from device to memory — CPU is **free to do other work**.
4. DMA controller interrupts CPU when the entire transfer is complete.

**Why DMA is important:** For large data transfers (e.g., reading a big file from disk), DMA avoids CPU involvement in every byte, dramatically improving performance.

**Priority:** ⭐⭐⭐⭐

---

## 5. Process Management

*College Foundation — Silberschatz Ch. 3*

### Program vs Process ⭐⭐⭐⭐⭐

| | Program | Process |
|--|---------|---------|
| **Definition** | Passive entity — a file on disk containing instructions | Active entity — a program in execution |
| **State** | Static, does not change | Dynamic, changes during execution |
| **Resources** | Just a file | Has CPU, memory, I/O resources allocated |
| **Example** | `chrome.exe` on disk | Chrome running in memory |

> A program becomes a process when it is **loaded into memory and executed**.
> The same program can create **multiple processes** (e.g., open Chrome twice → two processes).

### What a Process Consists Of

A process in memory has these sections:
```
+------------------+
|      Stack       |  ← function calls, local variables
+------------------+
|       ↓ ↑        |  ← stack grows down, heap grows up
+------------------+
|      Heap        |  ← dynamic memory (malloc, new)
+------------------+
|      Data        |  ← global/static variables
+------------------+
|      Text        |  ← program code (instructions)
+------------------+
```

### Process Control Block (PCB) ⭐⭐⭐⭐⭐

The **PCB** (also called Task Control Block) is the data structure the OS uses to represent and manage a process. Every process has exactly one PCB.

**PCB contains:**
- **Process ID (PID)** — unique identifier
- **Process State** — New / Ready / Running / Waiting / Terminated
- **Program Counter (PC)** — address of next instruction to execute
- **CPU Registers** — all register values (saved during context switch)
- **CPU Scheduling info** — priority, queue pointers
- **Memory management info** — page tables, base/limit registers
- **I/O status info** — list of open files, I/O devices in use
- **Accounting info** — CPU time used, process start time

**Why PCB matters:** When the OS switches the CPU from one process to another (context switch), it saves the current process's state into its PCB and loads the next process's state from its PCB.

### Process States ⭐⭐⭐⭐⭐

```
         admit
New ────────────→ Ready ←─────────────────────────────────┐
                    │                                      │
                    │ dispatch (scheduler picks it)        │
                    ↓                                      │
                 Running ───── I/O or event wait ───→ Waiting/Blocked
                    │                                      │
                    │ interrupt (preemption)               │ I/O complete
                    └──────────────────────────────────────┘
                    │
                    │ exit
                    ↓
                Terminated
```

| State | Meaning |
|-------|---------|
| **New** | Process is being created |
| **Ready** | In memory, waiting for CPU allocation |
| **Running** | Currently executing instructions on CPU |
| **Waiting/Blocked** | Waiting for I/O completion or an event |
| **Terminated** | Finished execution; OS cleaning up |

**Key transitions:**
- **Ready → Running:** Scheduler dispatches the process (gives it the CPU).
- **Running → Ready:** CPU is preempted (time quantum expired, higher-priority process arrives).
- **Running → Waiting:** Process requests I/O or waits for an event.
- **Waiting → Ready:** I/O completes or event occurs.

### Context Switch ⭐⭐⭐⭐⭐

**Definition:** The mechanism by which the OS saves the state of the currently running process and loads the state of the next process to run.

**Steps:**
1. OS saves current process state → PCB of current process.
2. OS selects next process to run (CPU scheduling).
3. OS loads state from PCB of next process → CPU registers.
4. CPU starts executing the new process.

**Context switch is pure overhead** — no useful work is done during the switch itself. That's why minimizing unnecessary context switches is important.

**Why thread context switch is cheaper than process context switch:**
- Threads share address space — no need to switch memory maps (page tables).
- Process switch requires switching the entire memory context — expensive.

### Process Scheduling

The OS maintains several queues:
- **Job Queue** — all processes in the system.
- **Ready Queue** — processes in memory, ready to execute.
- **Device Queues** — processes waiting for a specific I/O device.

**Schedulers:**
| Scheduler | Also Called | Role |
|-----------|-------------|------|
| Long-term | Job scheduler | Decides which programs are admitted to the ready queue |
| Short-term | CPU scheduler | Decides which ready process gets the CPU next |
| Medium-term | Swapper | Swaps processes in/out of memory (related to virtual memory) |

### Process Creation and Termination

**Creation (fork/exec model in Unix):**
- `fork()` — creates a new process (child) that is a copy of the parent.
- `exec()` — replaces the child process's memory with a new program.
- `wait()` — parent waits for child to finish.

**Termination:**
- `exit()` — process finishes and asks OS to delete it.
- Parent can terminate a child using `kill()`.
- **Zombie process:** Child has exited but parent hasn't called `wait()` yet. PCB remains.
- **Orphan process:** Parent exits before child. Init process (PID 1) adopts it.

**Priority:** ⭐⭐⭐⭐⭐

---

## 6. Threads

*College Foundation — Silberschatz Ch. 4 | Placement Extension: User/Kernel threads, models*

### What is a Thread? ⭐⭐⭐⭐⭐

**Simple Meaning:** A thread is the smallest unit of execution within a process. A process can have multiple threads, all sharing the same code, data, and open files — but each with its own program counter, registers, and stack.

**Why threads exist:** If you want multiple tasks to happen simultaneously *within the same application* (e.g., a browser downloading files while rendering a page), threads are the right tool.

### Single-threaded vs Multi-threaded Process

```
Single-threaded:                Multi-threaded:
+------------------+           +------------------+
|     Code         |           |     Code         |  ← shared
|     Data         |           |     Data         |  ← shared
|     Files        |           |     Files        |  ← shared
+------------------+           +------------------+
|  Thread (stack,  |           | Thread 1 | Thread 2 | Thread 3 |
|  registers, PC)  |           | (own     | (own     | (own     |
+------------------+           | stack,   | stack,   | stack,   |
                               | PC, regs)| PC, regs)| PC, regs)|
                               +------------------+
```

### Benefits of Multithreading ⭐⭐⭐⭐

1. **Responsiveness** — one thread can respond to user input while another does background work.
2. **Resource sharing** — threads share memory and files of the process; no need for IPC.
3. **Economy** — creating a thread is much cheaper than creating a process.
4. **Scalability** — threads can run on different CPU cores simultaneously (parallelism).

### Process vs Thread ⭐⭐⭐⭐⭐

| Feature | Process | Thread |
|---------|---------|--------|
| Definition | Program in execution | Execution unit within a process |
| Memory | Own separate address space | Shares process address space |
| Resources | Own resources (files, I/O) | Shares process resources |
| Communication | IPC (expensive) | Shared memory (fast, but needs sync) |
| Creation overhead | High | Low |
| Context switch | Expensive | Cheaper |
| Crash isolation | One process crash doesn't affect others | Crashed thread can crash whole process |
| Example | Chrome browser instance | Chrome tab loading (one thread per tab function) |

### User-Level Threads vs Kernel-Level Threads

| | User-Level Threads | Kernel-Level Threads |
|--|-------------------|---------------------|
| **Managed by** | Thread library (user space) | Operating system kernel |
| **Kernel awareness** | Kernel sees only one process | Kernel knows about each thread |
| **Context switch** | Very fast (no kernel involvement) | Slower (kernel mode switch) |
| **Blocking** | If one thread blocks, all block | If one thread blocks, others can run |
| **Parallelism** | Cannot use multiple CPUs | Can truly run on multiple CPUs |
| **Example** | Green threads, old Java threads | Windows threads, modern POSIX threads |

### Multithreading Models

These models define how user-level threads map to kernel-level threads:

**1. Many-to-One**
- Many user threads → one kernel thread.
- Simple but no parallelism; if one blocks, all block.
```
UThread UThread UThread
   ↘       ↓       ↙
      KThread
```

**2. One-to-One**
- Each user thread → one kernel thread.
- True parallelism; one blocking doesn't affect others.
- Creating many threads is expensive.
- Used by: Linux (pthreads), Windows.
```
UThread → KThread
UThread → KThread
UThread → KThread
```

**3. Many-to-Many**
- Many user threads → many (but fewer) kernel threads.
- Best of both worlds — parallelism + flexibility.
- Most complex to implement.
```
UThread UThread UThread UThread
    ↘     ↓         ↙
   KThread   KThread
```

**Priority:** ⭐⭐⭐⭐⭐

---

## 7. CPU Scheduling

*College Foundation + Placement Extension*

### Why CPU Scheduling? ⭐⭐⭐⭐⭐

A typical process alternates between **CPU bursts** (executing instructions) and **I/O bursts** (waiting for I/O). When a process waits for I/O, the CPU is idle — wasteful. The **CPU scheduler** picks another ready process to use the CPU during that time.

**Goal:** Maximize CPU utilization, keep the CPU busy, and minimize waiting times.

### Preemptive vs Non-preemptive Scheduling ⭐⭐⭐⭐⭐

| | Non-preemptive | Preemptive |
|--|---------------|-----------|
| **Meaning** | Once CPU is given, process keeps it until it finishes or blocks | OS can take CPU away at any time |
| **Context switch** | Only on voluntary yield or termination | Can happen at any point |
| **Fairness** | Lower — long jobs monopolize CPU | Higher |
| **Examples** | FCFS, SJF | SRTF, Round Robin, Priority (preemptive) |

### Scheduling Criteria (Metrics) ⭐⭐⭐⭐

| Metric | Meaning | Optimize |
|--------|---------|---------|
| **CPU Utilization** | % time CPU is busy | Maximize |
| **Throughput** | Processes completed per unit time | Maximize |
| **Turnaround Time** | Submission → completion | Minimize |
| **Waiting Time** | Total time in ready queue | Minimize |
| **Response Time** | Submission → first response | Minimize |

**Formulas:**
```
Turnaround Time (TAT) = Completion Time - Arrival Time
Waiting Time (WT)     = Turnaround Time - Burst Time
Response Time         = Time of first CPU allocation - Arrival Time
```

---

### FCFS — First Come, First Served ⭐⭐⭐⭐

**Idea:** Processes are served in the order they arrive. The CPU is never taken away (non-preemptive).

**Example:**

| Process | Arrival | Burst |
|---------|---------|-------|
| P1 | 0 | 10 |
| P2 | 1 | 5 |
| P3 | 2 | 8 |

```
Gantt: | P1(0-10) | P2(10-15) | P3(15-23) |
WT: P1=0, P2=9, P3=13  → Avg WT = 7.3
```

**Convoy Effect:** A long process holds the CPU while shorter processes queue behind it — like a slow truck blocking a highway. Greatly reduces throughput.

**Disadvantage:** Convoy effect; not good for time-sharing systems.

---

### SJF — Shortest Job First ⭐⭐⭐⭐

**Idea:** Pick the process with the shortest CPU burst time. Provably **optimal** for minimizing average waiting time.

**Type:** Non-preemptive (SJF) — once started, a process runs to completion.

**Problem:** Requires knowing burst times in advance — usually not possible. In practice, burst time is **estimated** using exponential averaging of past behavior.

**Starvation:** Long processes may never get the CPU if short ones keep arriving.

---

### SRTF — Shortest Remaining Time First ⭐⭐⭐⭐

**Idea:** Preemptive version of SJF. At every arrival of a new process, compare its burst with the remaining time of the current process. If new process is shorter, preempt.

**Advantage:** Minimum average waiting time (optimal among all preemptive algorithms).
**Disadvantage:** High context switch overhead; starvation of long processes.

---

### Round Robin (RR) ⭐⭐⭐⭐⭐

**Idea:** Each process gets a fixed **time quantum** (q). After q milliseconds, if the process hasn't finished, it is preempted and placed at the back of the ready queue.

**Example (q = 4):**

| Process | Burst |
|---------|-------|
| P1 | 10 |
| P2 | 5 |
| P3 | 8 |

```
Gantt: |P1(0-4)|P2(4-8)|P3(8-12)|P1(12-16)|P2(16-17)|P3(17-21)|P1(21-27)|
```

**Effect of quantum size:**
- **q too small** → many context switches → high overhead, low throughput.
- **q too large** → degenerates to FCFS.
- **Rule of thumb:** q should be slightly larger than a typical CPU burst.

**Advantages:** Fair, good response time for interactive systems. Used in all modern general-purpose OS.
**Disadvantage:** Higher average TAT than SJF.

---

### Priority Scheduling ⭐⭐⭐⭐

**Idea:** Each process has a priority. CPU goes to the highest-priority process.

- **Preemptive variant:** If a higher-priority process arrives, it immediately preempts the current one.
- **Non-preemptive variant:** Current process finishes, then highest priority is picked.

**Starvation:** Low-priority processes may never run if high-priority ones keep arriving.

**Aging:** Solution to starvation. Gradually increase the priority of processes that have been waiting for a long time. Eventually they become high enough priority to run.

**Note:** SJF is a special case of priority scheduling where priority = 1/burst_time.

---

### MLFQ — Multi-Level Feedback Queue ⭐⭐⭐⭐

**Idea:** Multiple queues with different priorities. Processes move between queues based on their behavior.

- New processes start in the **highest-priority queue**.
- If a process uses its full quantum, it is moved to a **lower-priority queue** (CPU-bound).
- If a process blocks for I/O before using its quantum, it stays or moves up (I/O-bound).
- Lower queues typically get larger time quanta.

**Goal:** Favor short and I/O-bound processes automatically, without knowing burst times in advance.

**Why it works:** Interactive (I/O-bound) processes naturally bubble up to high-priority queues. CPU-bound processes settle into low-priority queues with large quanta.

**Used by:** Most modern OS (Windows, Linux).

---

### Summary Comparison

| Algorithm | Preemptive | Starvation | Best For |
|-----------|-----------|-----------|---------|
| FCFS | No | No | Batch systems |
| SJF | No | Yes (long jobs) | Minimum avg WT (theory) |
| SRTF | Yes | Yes (long jobs) | Minimum avg WT (preemptive) |
| Round Robin | Yes | No | Time-sharing, interactive |
| Priority | Both | Yes (low priority) | Systems with varied priorities |
| MLFQ | Yes | Possible | General-purpose OS |

**Priority:** ⭐⭐⭐⭐⭐

---

## 8. Process Synchronization

*College Foundation + Placement Extension*

### Why Synchronization? ⭐⭐⭐⭐⭐

When multiple processes/threads share data and execute concurrently, they can interfere with each other in unpredictable ways — producing **incorrect results** that depend on the exact order of execution.

```
Thread 1: counter = counter + 1
Thread 2: counter = counter + 1

If counter = 5 initially:
  Expected result: counter = 7
  Possible wrong result: counter = 6 (if both read 5, both write 6)
```

This is because `counter = counter + 1` is not a single atomic operation — it involves read, modify, write. A context switch between any of these steps causes the problem.

### Race Condition ⭐⭐⭐⭐⭐

**Definition:** A race condition occurs when the outcome of a program depends on the sequence or timing of uncontrollable events like thread scheduling. Multiple threads access shared data concurrently and at least one modifies it.

### Critical Section ⭐⭐⭐⭐⭐

**Definition:** A segment of code that accesses shared resources (shared variables, files, etc.) and must not be executed by more than one process/thread at a time.

**Structure of a process with critical section:**
```
do {
    [entry section]     ← request permission to enter
        critical section
    [exit section]      ← signal that you are leaving
        remainder section
} while (true);
```

### Three Requirements for a Valid Critical Section Solution ⭐⭐⭐⭐⭐

1. **Mutual Exclusion** — If process Pi is in the critical section, no other process can be in it.
2. **Progress** — If no process is in the critical section and some processes want to enter, the decision of who enters next cannot be postponed indefinitely.
3. **Bounded Waiting** — A process that has requested to enter the critical section must be granted entry within a bounded number of attempts by other processes (no starvation).

### Mutex (Mutual Exclusion Lock) ⭐⭐⭐⭐⭐

**Simple Meaning:** A mutex is a lock. A thread acquires (locks) the mutex before entering the critical section and releases (unlocks) it when leaving. Only the thread that locked it can unlock it.

**Key property:** Ownership — the thread that acquires a mutex is the only one that can release it.

```
acquire(mutex)
    critical section
release(mutex)
```

**Busy waiting:** If a thread finds the mutex locked, it may loop repeatedly checking ("spin") until it becomes free. This wastes CPU. Can be avoided with **blocking** (thread sleeps until mutex is available).

**Spinlock:** A mutex implemented with busy waiting. Good for very short critical sections on multi-core systems where spinning is cheaper than the context switch overhead.

### Semaphore ⭐⭐⭐⭐⭐

**Simple Meaning:** A semaphore is an integer variable with two atomic operations: `wait` (P) and `signal` (V).

```
wait(S):    while S <= 0 do nothing;
            S = S - 1;

signal(S):  S = S + 1;
```

**Binary Semaphore:** Value is 0 or 1 — behaves like a mutex.

**Counting Semaphore:** Value can be any non-negative integer — used to control access to a pool of N identical resources.
- Initialized to N (number of available resources).
- Each `wait` decrements (takes a resource); each `signal` increments (returns a resource).

### Mutex vs Semaphore ⭐⭐⭐⭐⭐

| Feature | Mutex | Semaphore |
|---------|-------|-----------|
| Type | Locking mechanism | Signaling mechanism |
| Ownership | Yes — only locker can unlock | No ownership |
| Value | Binary (locked/unlocked) | Any non-negative integer |
| Use case | Mutual exclusion (one at a time) | Resource counting (N at a time) |
| Signaling | Cannot signal without owning | Any thread can signal |
| Example | One writer at a time | Allow up to 5 DB connections |

**Critical distinction:** A semaphore can be signaled by a *different* thread than the one that waited on it — this makes semaphores useful for **signaling** (e.g., producer signals consumer). A mutex can only be released by the thread that acquired it.

### Classic Synchronization Problems ⭐⭐⭐⭐

#### Producer-Consumer Problem
- Shared bounded buffer.
- Producer adds items; Consumer removes items.
- Problem: Producer must not add to a full buffer; Consumer must not remove from an empty buffer.
- Solution: Use semaphores: `empty` (initially N), `full` (initially 0), `mutex` (initially 1).

```
Producer:
  wait(empty)   // wait for space
  wait(mutex)   // exclusive access
  add item
  signal(mutex)
  signal(full)  // signal item available

Consumer:
  wait(full)    // wait for item
  wait(mutex)
  remove item
  signal(mutex)
  signal(empty) // signal space available
```

#### Reader-Writer Problem
- Multiple readers can read simultaneously.
- A writer needs exclusive access (no readers or writers).
- Problem: Writers may starve if readers keep arriving.
- Solution variants: Reader-preference (writers can starve) or Writer-preference (readers can starve).

#### Dining Philosophers Problem
- 5 philosophers sit around a table, each needs 2 forks to eat.
- 5 forks between them.
- Problem: If all pick up left fork simultaneously → deadlock (circular wait).
- Solutions: Allow only 4 philosophers at table at once; or pick up both forks atomically; or asymmetric ordering.

**Priority:** ⭐⭐⭐⭐⭐

---

## 9. Deadlocks

*College Foundation + Placement Extension*

### What is a Deadlock? ⭐⭐⭐⭐⭐

**Definition:** A set of processes is deadlocked when every process in the set is waiting for a resource held by another process in the set — so no process can proceed, ever.

**Real-world analogy:** Two cars on a narrow one-lane bridge from opposite sides. Each waits for the other to move back — neither can proceed.

```
P1 holds R1, needs R2
P2 holds R2, needs R1
→ Both wait forever = Deadlock
```

### Four Coffman Conditions ⭐⭐⭐⭐⭐

**All four must hold simultaneously for deadlock to occur. Eliminate any one → deadlock impossible.**

1. **Mutual Exclusion** — At least one resource is non-shareable (only one process can use it at a time).
2. **Hold and Wait** — A process holds at least one resource and is waiting to acquire additional resources held by other processes.
3. **No Preemption** — Resources cannot be forcibly taken from a process; a process must release resources voluntarily.
4. **Circular Wait** — A cycle exists in the wait graph: P1 waits for P2, P2 waits for P3, ..., Pn waits for P1.

**Memory trick:** **M**y **H**orse **N**ever **C**ircles (Mutual exclusion, Hold & wait, No preemption, Circular wait)

### Resource Allocation Graph (RAG)

A directed graph to detect deadlock:
- **Circle** = Process
- **Rectangle** = Resource (dots inside = instances)
- **Arrow from Process → Resource** = process is requesting resource
- **Arrow from Resource → Process** = resource is assigned to process

**Rule:** If the RAG contains a **cycle**:
- Single-instance resources: cycle **→ deadlock definitely exists**.
- Multi-instance resources: cycle **→ deadlock may exist** (need further analysis).

No cycle → no deadlock.

### Deadlock Handling Strategies ⭐⭐⭐⭐⭐

| Strategy | Approach | Cost |
|----------|---------|------|
| **Prevention** | Eliminate one of the 4 conditions | Conservative; may under-utilize resources |
| **Avoidance** | Grant resources only if safe state is maintained | Need advance info; moderate overhead |
| **Detection + Recovery** | Allow deadlock; detect it; recover | Detection overhead; recovery may disrupt processes |
| **Ignorance** | Ignore deadlock (Ostrich Algorithm) | Used in some OS (e.g., early Unix); acceptable if rare |

### Deadlock Prevention ⭐⭐⭐⭐

Eliminate at least one Coffman condition:

| Condition | How to Prevent | Drawback |
|-----------|----------------|---------|
| Mutual Exclusion | Make resources shareable (not always possible) | Cannot always share (e.g., printer) |
| Hold and Wait | Process requests all resources at once | Low resource utilization; starvation |
| No Preemption | Preempt resources from waiting processes | Data inconsistency risk |
| Circular Wait | Impose ordering on resources; request in order | Restrictive; hard to implement |

### Deadlock Avoidance ⭐⭐⭐⭐⭐

**Key concept: Safe State**

A system is in a **safe state** if there exists a **safe sequence** in which every process can eventually complete, even if all request their maximum remaining resources. In a safe state, no deadlock will occur.

- **Safe State → No Deadlock** (not necessarily the other way)
- **Unsafe State → Deadlock POSSIBLE** (not certain)

**Banker's Algorithm** (Dijkstra):

The most famous deadlock avoidance algorithm. Before granting a resource request, the OS simulates the allocation and checks if the resulting state is safe. If safe → grant. If unsafe → make process wait.

**Data structures:**
- `Available[j]` — number of available instances of resource type j
- `Max[i][j]` — max demand of process i for resource j
- `Allocation[i][j]` — currently allocated to process i
- `Need[i][j]` = Max[i][j] - Allocation[i][j]

**Safety Algorithm:** Find a safe sequence by repeatedly finding a process whose Need ≤ Available, "run" it, release its resources, repeat. If all processes can complete → safe state.

**Limitation:** Requires processes to declare maximum resource needs in advance — not always practical.

### Deadlock Detection ⭐⭐⭐⭐

For single-instance resources: Check RAG for cycles.
For multi-instance resources: Use an algorithm similar to Banker's safety check.

**Detection overhead** increases with detection frequency. Run too often → overhead. Run too rarely → long deadlock period.

### Deadlock Recovery

After detection, the OS must break the deadlock:

1. **Process Termination:**
   - Abort all deadlocked processes (brute force).
   - Abort one at a time until deadlock is broken (less disruptive).

2. **Resource Preemption:**
   - Forcibly take a resource from one process and give it to another.
   - Must handle rollback — preempted process may need to restart.
   - Must prevent starvation — same process shouldn't always be the victim.

### Deadlock vs Starvation ⭐⭐⭐⭐

| | Deadlock | Starvation |
|--|---------|-----------|
| **Cause** | Circular wait for resources | Scheduling — lower priority never gets CPU |
| **Processes** | All deadlocked processes are blocked | Starving process is blocked, others run |
| **Waiting for** | Each other (resources) | CPU to be scheduled |
| **Resolution** | Kill process, preempt resources | Aging (increase priority over time) |
| **Solution** | Prevention, avoidance, detection | Aging in scheduler |

### Livelock

Processes keep responding to each other but make no progress. Neither is blocked, but neither proceeds either.

**Analogy:** Two people in a hallway, both stepping the same direction to get out of each other's way — repeatedly — but never actually passing.

**Priority:** ⭐⭐⭐⭐⭐

---

## 10. Memory Management

*College Foundation + Placement Extension*

### Why Memory Management? ⭐⭐⭐⭐

Multiple processes must coexist in memory simultaneously. The OS must:
- Allocate memory to processes when they start.
- Deallocate when they finish.
- Protect each process's memory from others.
- Handle memory efficiently (avoid waste).

### Logical vs Physical Address ⭐⭐⭐⭐⭐

| | Logical Address | Physical Address |
|--|----------------|-----------------|
| **Also called** | Virtual address | Real address |
| **Generated by** | CPU / program | Memory hardware (RAM) |
| **Seen by** | Programmer / process | Memory controller |
| **Meaning** | Address in process's virtual view | Actual location in RAM |

The **Memory Management Unit (MMU)** is hardware that translates logical addresses → physical addresses at runtime.

**Address Binding:** The process of mapping logical addresses to physical addresses.
- **Compile time** — if address known at compile time (absolute code).
- **Load time** — relocatable code; binding at load.
- **Execution time** — binding happens at runtime (modern OS, uses MMU).

### Contiguous Memory Allocation

Each process occupies a **single continuous block** of physical memory.

**Fixed Partitioning:**
- Memory divided into fixed-size partitions.
- Each partition holds one process.
- **Internal fragmentation** — partition is bigger than the process, wasted space inside.

**Variable Partitioning:**
- Partitions sized exactly to process needs.
- **External fragmentation** — over time, free memory gets scattered in small non-contiguous holes.
- **Compaction** — move all processes to one end, consolidate free space. Expensive.

**Allocation strategies for variable partitioning:**
- **First Fit** — allocate first hole that is big enough. Fast.
- **Best Fit** — allocate smallest hole that fits. Minimizes wasted space, but slow; creates tiny useless holes.
- **Worst Fit** — allocate largest hole. Leaves largest possible remainder; poor performance.

### Fragmentation ⭐⭐⭐⭐⭐

| | Internal Fragmentation | External Fragmentation |
|--|----------------------|----------------------|
| **Definition** | Allocated memory > required memory; wasted space inside allocated block | Total free memory is enough, but scattered; no single block is large enough |
| **Cause** | Fixed-size partitions or pages slightly larger than needed | Variable allocation over time |
| **Solution** | Smaller pages/blocks | Compaction, paging, segmentation |

### Paging ⭐⭐⭐⭐⭐

**Simple Meaning:** Break both physical memory and logical memory into fixed-size blocks. No need for contiguous allocation.

- **Physical memory** is divided into fixed-size blocks called **frames**.
- **Logical memory** is divided into same-size blocks called **pages**.
- Page size = Frame size (typically 4KB).

**How it works:**
```
Logical Address: | Page Number (p) | Page Offset (d) |
                        ↓ (page table lookup)
Physical Address: | Frame Number (f) | Offset (d) |
```

**Page Table:** OS maintains one page table per process. Each entry maps page number → frame number.

```
Logical Address → [Page Table] → Physical Address
     p=2, d=100     frame[2]=5     5*4096 + 100
```

**Advantages:**
- No external fragmentation (pages fit into any free frame).
- Easy memory allocation.
- Simple memory protection (each page can have read/write/execute bits).

**Disadvantages:**
- Internal fragmentation (last page may not be full).
- Page table can be large (needs memory itself).
- Two memory accesses per data access (page table + data) — solved by TLB.

**Multi-level Page Tables:**
For large address spaces, a single page table is huge. Split the page number into multiple levels.
- Two-level: Outer page table → Inner page table → Frame.
- Reduces memory for page tables when most of the address space is unused.

**Inverted Page Table:**
One entry per physical frame (not per logical page). Reduces page table size at the cost of slower lookup. Used in IBM and PowerPC.

### Segmentation ⭐⭐⭐⭐

**Idea:** Divide a process's memory into variable-size **segments** based on logical units: code segment, data segment, stack segment, heap segment.

**Each segment has:**
- **Base** — starting physical address.
- **Limit** — size of the segment.

**Segment Table:** Maps segment number → (base, limit).

**Addressing:**
```
Logical Address: | Segment Number (s) | Offset (d) |
Segment Table[s] gives (base, limit)
Physical Address = base + d  (if d < limit, else trap)
```

**Advantages:**
- Matches programmer's view — code, data, stack are separate.
- No internal fragmentation.
- Easy to share segments between processes.

**Disadvantages:**
- External fragmentation (variable-size segments leave holes).

### Paging vs Segmentation ⭐⭐⭐⭐⭐

| Feature | Paging | Segmentation |
|---------|--------|-------------|
| Division unit | Fixed-size pages | Variable-size segments |
| Division basis | Physical (hardware convenience) | Logical (programmer's view) |
| Internal fragmentation | Yes (last page may be partially used) | No |
| External fragmentation | No | Yes |
| Programmer visibility | Transparent — programmer unaware | Visible — code/data/stack are segments |
| Address format | Page number + offset | Segment number + offset |
| Table type | Page table (one entry per page) | Segment table (one entry per segment) |

**Note:** Modern systems often combine both — **segmented paging** (segments divided into pages).

**Priority:** ⭐⭐⭐⭐⭐

---

## 11. Virtual Memory

*Placement Extension*

### What is Virtual Memory? ⭐⭐⭐⭐⭐

**Simple Meaning:** Virtual memory allows a process to use more memory than physically available in RAM, by using disk space as an extension of RAM.

**Technical Definition:** Virtual memory is a memory management technique that provides an abstraction of the storage resources available to a process. Each process gets a large virtual address space, only portions of which need to be in physical memory at any time.

**Key benefit:** Allows running programs larger than physical RAM. Enables more processes to run simultaneously by keeping only active pages in RAM.

### Demand Paging ⭐⭐⭐⭐⭐

**Idea:** Don't load all of a process's pages into memory at start. Load a page into memory only when it is **demanded** (accessed).

- Pages not in memory are kept on **disk** (swap space / page file).
- Each page table entry has a **valid/invalid bit**:
  - Valid (1) → page is in memory.
  - Invalid (0) → page is on disk.

### Page Fault ⭐⭐⭐⭐⭐

**Definition:** When a process accesses a page that is not currently in physical memory (valid bit = 0), the hardware generates a **page fault** — a trap to the OS.

**Page Fault Handling:**
```
1. Process accesses a page → check page table
2. Valid bit = 0 → Page Fault trap → OS takes control
3. OS finds the page on disk (swap space)
4. OS finds a free frame in RAM (or evicts a page using replacement algorithm)
5. OS reads the page from disk into the free frame
6. OS updates the page table (set valid bit = 1, set frame number)
7. Restart the instruction that caused the page fault
8. Process continues normally
```

**Page fault is expensive** — disk access takes milliseconds vs nanoseconds for RAM. Minimizing page faults is critical.

### TLB — Translation Lookaside Buffer ⭐⭐⭐⭐⭐

**Problem:** Every memory access with paging requires two memory accesses — first to the page table, then to the actual data. This doubles memory access time.

**Solution:** TLB — a small, fast hardware cache that stores recent page-to-frame mappings.

```
Logical Address → Check TLB → TLB Hit → Physical Address (fast)
                           ↓ TLB Miss
               → Page Table in RAM → Physical Address (slow)
               → Update TLB with new mapping
```

| | TLB Hit | TLB Miss |
|--|---------|---------|
| **What happens** | Frame found in TLB directly | Must access page table in RAM |
| **Speed** | Very fast (1-2 CPU cycles) | Slow (full memory access for page table) |

**Effective Access Time (EAT):**
```
EAT = (Hit Ratio × TLB access time) + ((1 - Hit Ratio) × (TLB + memory access time))
```

If hit ratio = 90%, TLB access = 1ns, memory access = 100ns:
```
EAT = 0.9 × (1 + 100) + 0.1 × (1 + 100 + 100) = 90.9 + 20.1 = 111ns
Without TLB: 200ns (two memory accesses)
```

### Page Replacement Algorithms ⭐⭐⭐⭐⭐

When a page fault occurs and no free frame is available, the OS must **evict** (replace) a page from RAM to make room.

#### FIFO (First In, First Out)

Replace the page that has been in memory the **longest**.
Simple to implement. **Belady's Anomaly** can occur — adding more frames can actually increase page faults!

#### Optimal (OPT)

Replace the page that will **not be used for the longest time in the future**.
Provably optimal (minimum page faults), but **not implementable** (requires knowing future references). Used as a benchmark.

#### LRU — Least Recently Used ⭐⭐⭐⭐⭐

Replace the page that was **least recently used** (used farthest in the past).
Best practical algorithm. Assumption: recently used pages will be used again soon (temporal locality).
Implementation: Use a counter (timestamp) or a stack. Can be expensive in hardware.

#### LFU — Least Frequently Used

Replace the page with the lowest access count. Problem: a page heavily used in the past but no longer needed has a high count and is hard to replace.

#### Second Chance / Clock Algorithm

A modification of FIFO. Each page has a **reference bit** (set to 1 when accessed). When a page is chosen for replacement, if reference bit = 1, reset to 0 and move on (give second chance). Replace only pages with reference bit = 0.

#### Belady's Anomaly

With FIFO replacement, increasing the number of frames can **increase** the number of page faults. (Counter-intuitive!)
OPT and LRU do not suffer from Belady's Anomaly — they are **stack algorithms**.

### Thrashing ⭐⭐⭐⭐⭐

**Definition:** A condition where the OS spends more time paging (moving pages between RAM and disk) than executing actual process instructions. CPU utilization drops dramatically.

**Cause:**
- Process has fewer frames than its working set needs.
- Every access causes a page fault.
- OS constantly swapping pages in and out.

**Detection:** CPU utilization drops despite many processes being active.

**Solutions:**
- **Working Set Model:** Allocate enough frames to hold each process's working set (set of pages actively used in recent Δ time window).
- **Page Fault Frequency (PFF):** Monitor page fault rate. If too high → give more frames. If too low → take frames away.
- Reduce degree of multiprogramming (swap out some processes entirely).

```
Thrashing chain:
Too many processes
  → Not enough frames per process
    → Constant page faults
      → OS busy paging, not executing
        → CPU utilization collapses
          → OS admits more processes (to raise utilization) → worse
```

**Priority:** ⭐⭐⭐⭐⭐

---

## 12. File Systems

*Placement Extension*

### What is a File? ⭐⭐⭐⭐

A **file** is a named collection of related information stored on secondary storage. From the user's view, a file is the smallest unit of storage. The OS abstracts the physical storage into a uniform file model.

**File Attributes:**
- Name (human-readable), Identifier (unique ID), Type, Location (disk pointer), Size, Protection, Timestamps.

**File Operations:**
`create`, `read`, `write`, `reposition (seek)`, `delete`, `truncate`

The OS maintains an **open-file table** for each process. `open()` returns a **file descriptor** (integer) used for subsequent operations.

### Directory ⭐⭐⭐

A directory organizes files. It is itself a file containing a list of names → file attributes/pointers.

**Directory Structures:**

| Structure | Description | Example |
|-----------|-------------|---------|
| **Single-level** | All files in one directory | Early MS-DOS |
| **Two-level** | Separate directory per user | Basic Unix |
| **Tree-structured** | Hierarchical, arbitrary depth | Modern OS |
| **Acyclic-graph** | Allow sharing (hard links) | Unix with links |
| **General graph** | Cycles possible; need cycle handling | Complex; rare |

### File Allocation Methods ⭐⭐⭐⭐⭐

How files are physically stored on disk:

#### Contiguous Allocation
- File occupies consecutive disk blocks.
- Fast sequential and random access.
- **External fragmentation** over time.
- Hard to grow files.

#### Linked Allocation
- Each block contains a pointer to the next block.
- No external fragmentation; files can grow easily.
- **Slow random access** — must follow links from start.
- **FAT (File Allocation Table):** Linked allocation with the link table stored separately in memory for faster access. Used by FAT32 (USB drives).

#### Indexed Allocation
- A special **index block** holds pointers to all data blocks of the file.
- Supports direct random access; no external fragmentation.
- **Index block overhead** — wastes space for small files.
- **Unix inode:** An index block (with direct, indirect, double-indirect, triple-indirect pointers). Supports both small and very large files.

| Method | Sequential Access | Random Access | External Frag | Internal Frag |
|--------|-----------------|---------------|--------------|--------------|
| Contiguous | Fast | Fast | Yes | Minor |
| Linked | OK | Slow | No | Minor |
| Indexed | Fast | Fast | No | Yes (index block) |

### inode (Index Node) ⭐⭐⭐⭐

An **inode** is a data structure in Unix-like file systems that stores metadata about a file:
- File type, permissions, owner, size, timestamps.
- **Direct pointers** → first ~12 data blocks (small files — fast).
- **Single indirect pointer** → block of pointers → more data blocks.
- **Double indirect pointer** → block of pointers to blocks of pointers.
- **Triple indirect** → for very large files.

**Note:** An inode does NOT store the filename — filenames are in directory entries that point to inodes.

### Hard Link vs Soft (Symbolic) Link ⭐⭐⭐⭐

| | Hard Link | Soft (Symbolic) Link |
|--|-----------|---------------------|
| **Points to** | Same inode (same file data) | Path/filename of another file |
| **If original deleted** | File still accessible via hard link | Soft link becomes a dangling link |
| **Cross filesystem** | No | Yes |
| **File type** | Same as original | Special link file |
| **inode** | Same inode number | Different inode |

**Free Space Management:**
- **Bit vector (bitmap):** One bit per block; 0 = free, 1 = occupied. Simple, efficient.
- **Linked list:** Free blocks linked together. Finding a free block requires traversal.
- **Grouping / Counting:** Variants that speed up finding multiple free blocks.

**Priority:** ⭐⭐⭐⭐

---

## 13. Mass Storage & Disk Scheduling

*Placement Extension*

### Magnetic Disk Structure

```
Disk surface
  → Tracks (concentric circles)
     → Sectors (divisions of a track)
```

**Access time components:**
- **Seek time** — moving the read/write head to the correct track (slowest).
- **Rotational latency** — waiting for the sector to rotate under the head.
- **Transfer time** — actually reading/writing the data.

Seek time dominates. **Disk scheduling** algorithms minimize total head movement → reduce seek time.

### Disk Scheduling Algorithms ⭐⭐⭐⭐

**Example:** Disk has cylinders 0-199. Head starts at cylinder 53.
Request queue: 98, 183, 37, 122, 14, 124, 65, 67

#### FCFS
Service requests in arrival order.
```
53 → 98 → 183 → 37 → 122 → 14 → 124 → 65 → 67
Total movement: 640 cylinders
```
Simple but high head movement.

#### SSTF (Shortest Seek Time First)
Always service the nearest request first.
```
53 → 65 → 67 → 37 → 14 → 98 → 122 → 124 → 183
Total movement: 236 cylinders
```
Better than FCFS. **Starvation** possible for requests far from current head position.

#### SCAN (Elevator Algorithm)
Head moves in one direction, services all requests in path, reaches end, reverses.
```
53 → 65 → 67 → 98 → 122 → 124 → 183 → 199 (end) → 37 → 14
```
No starvation. Requests near the end of the disk wait longer.

#### C-SCAN (Circular SCAN)
Head moves in one direction only, services requests, jumps back to beginning (without servicing on return), then sweeps again.
Provides more uniform wait times than SCAN.

#### LOOK and C-LOOK
Like SCAN and C-SCAN, but the head only goes **as far as the last request** in each direction — doesn't travel all the way to the disk end.

| Algorithm | Movement | Starvation | Notes |
|-----------|----------|-----------|-------|
| FCFS | High | No | Simple, fair order |
| SSTF | Better | Yes | Greedy; near requests favored |
| SCAN | Good | No | Elevator; visits all |
| C-SCAN | Good | No | Uniform wait time |
| LOOK/C-LOOK | Best | No | SCAN without unnecessary travel |

**Used in practice:** C-LOOK (or variants) for HDDs. SSDs do not need disk scheduling (no mechanical head).

**Priority:** ⭐⭐⭐

---

## 14. Caching & Storage Hierarchy

*College Foundation + Placement Extension*

### Storage Hierarchy ⭐⭐⭐⭐

```
Fastest, Smallest, Most Expensive
┌────────────────────────┐
│  CPU Registers          │  ← ~1 ns, bytes
├────────────────────────┤
│  Cache (L1, L2, L3)     │  ← 1-10 ns, KB-MB
├────────────────────────┤
│  Main Memory (RAM)      │  ← ~100 ns, GB
├────────────────────────┤
│  Secondary Storage (SSD)│  ← ~0.1 ms, TB
├────────────────────────┤
│  Secondary Storage (HDD)│  ← ~10 ms, TB
├────────────────────────┤
│  Tertiary (Tape/Optical)│  ← seconds, PB
└────────────────────────┘
Slowest, Largest, Cheapest
```

**Trade-off:** Speed ↑ → Cost ↑, Capacity ↓.

### Cache ⭐⭐⭐⭐

**Definition:** A smaller, faster storage that holds a copy of frequently accessed data from a larger, slower storage.

**Cache Hit:** Requested data is found in the cache → fast access.
**Cache Miss:** Requested data is NOT in cache → fetch from slower storage, load into cache → slower.

**Why caching works:** Programs exhibit **locality of reference**:
- **Temporal locality:** A recently accessed item is likely to be accessed again soon (same instruction in a loop).
- **Spatial locality:** Items near a recently accessed item are likely to be accessed soon (next elements in an array).

### Data Migration

Information is copied between levels of the hierarchy when needed:
- CPU registers ← Cache ← RAM ← Disk
- Each level caches data from the level below it.
- **Consistency problem:** If multiple caches hold copies of the same data (in multiprocessor systems), keeping them consistent is the **cache coherency** problem.

**Priority:** ⭐⭐⭐

---

## 15. Protection & Security

*Placement Extension*

### Protection vs Security ⭐⭐⭐⭐

| | Protection | Security |
|--|-----------|---------|
| **Focus** | Internal — controlling access of processes/users to resources within the OS | External — defending against outside threats (malware, unauthorized access) |
| **Goal** | Ensure each process/user accesses only what it is authorized to | Prevent unauthorized access, data breaches, attacks |
| **Scope** | Mechanisms within the OS (permissions, access control) | Broader — includes authentication, encryption, network security |
| **Example** | A process cannot read another process's memory | Firewall blocking a network intrusion |

### Access Control ⭐⭐⭐⭐

**User ID (UID):** Unique identifier for each user. Determines what resources the user can access.

**Group ID (GID):** Users can belong to groups; permissions can be set per group.

**Unix File Permissions:**
```
-rwxr-xr--  owner  group  others
```
- **r** = read, **w** = write, **x** = execute
- Three sets of permissions: owner / group / others

**Access Control List (ACL):** A list associated with each resource specifying which users/groups have which types of access. More fine-grained than Unix permissions.

### Protection Mechanisms

**Goal of protection:** Prevent accidental or deliberate misuse of resources.

Key mechanisms in the OS:

1. **User Mode / Kernel Mode** — User programs cannot directly access hardware. Must request through system calls. Hardware enforces this boundary via the mode bit.

2. **Memory Protection** — Base and limit registers prevent a process from accessing memory outside its own address space. Page table entries include protection bits (read/write/execute).

3. **Timer** — Hardware timer interrupts the CPU periodically, preventing any single process from running forever (monopolizing CPU). The OS regains control via the timer interrupt.

4. **Privilege Escalation** — Running as a lower-privilege user, then legitimately (via authentication) acquiring higher privileges (e.g., `sudo` in Linux).

### Why OS Needs Protection

- Prevent buggy programs from corrupting other programs' data.
- Prevent malicious programs from accessing unauthorized resources.
- Ensure fair use of shared resources.
- Maintain system stability.

**The OS is the enforcer** — it uses hardware features (mode bit, MMU, timer) as its tools.

**Priority:** ⭐⭐⭐

---

## 16. Types of Operating Systems

*College Foundation — Silberschatz Ch. 1*

### Batch Systems

**Definition:** Jobs are collected, grouped into batches, and executed without user interaction. Users submit jobs; results come back later.

**Characteristic:** No real-time user interaction. Jobs run to completion before next starts.
**Objective:** Maximum CPU utilization, throughput.
**Issue:** No interactivity; if a job has an error, user only finds out after the batch completes.

### Multiprogramming ⭐⭐⭐⭐⭐

**Definition:** Multiple programs loaded into memory simultaneously. When one program waits for I/O, the CPU switches to another.

**Key idea:** Keep the CPU busy — if one process is waiting (I/O), run another.
**Objective:** Maximize CPU utilization.
**Requirement:** Memory management (to hold multiple programs) and CPU scheduling.

> **Multiprogramming ≠ Parallel execution.** Only one process runs on the CPU at any time; the others wait. But the CPU is never idle unnecessarily.

### Multitasking / Time-Sharing ⭐⭐⭐⭐⭐

**Definition:** Extension of multiprogramming where the CPU switches between jobs so **frequently** that each user gets the impression of having a dedicated machine.

**Characteristic:** Multiple users interact with the system simultaneously. Response time is short (< 1 second).
**Mechanism:** Round-Robin CPU scheduling with small time quanta.
**Objective:** Responsiveness, user interaction.

| | Multiprogramming | Time-Sharing (Multitasking) |
|--|-----------------|---------------------------|
| Goal | CPU utilization | User response time |
| Switching | On I/O (process voluntarily gives up CPU) | Frequent, time-driven (preemptive) |
| Users | Typically batch | Multiple interactive users |

### Multiprocessing (Parallel Systems) ⭐⭐⭐⭐

**Definition:** Multiple CPUs (processors) in a single system, sharing memory and I/O devices.

**Types:**
- **Symmetric Multiprocessing (SMP):** All CPUs are equal; each runs the same OS. Most common today (modern multi-core CPUs). CPUs share a single memory.
- **Asymmetric Multiprocessing:** One master CPU controls the system; others execute assigned tasks.

**Advantage:** Increased throughput, fault tolerance (if one CPU fails, others continue).

**Multiprocessing ≠ Multitasking:** Multiprocessing means multiple CPUs; multitasking means multiple tasks on (potentially) one CPU.

### Distributed Systems ⭐⭐⭐

**Definition:** A collection of physically separate computers, each with its own memory, connected via a network — appearing as a single coherent system to users.

**Characteristic:** No shared memory between computers; communicate via message passing.
**Objective:** Resource sharing, load balancing, fault tolerance, geographic distribution.
**Example:** Cloud computing, distributed databases.

**Parallel vs Distributed:**

| | Parallel (Multiprocessing) | Distributed |
|--|--------------------------|------------|
| Memory | Shared | Not shared |
| Location | Same machine | Different machines |
| Communication | Shared memory | Network (message passing) |
| Example | Multi-core CPU | Cloud servers |

### Real-Time Systems ⭐⭐⭐⭐

**Definition:** Systems where correctness depends not only on logical result but also on **time** — results must be produced within a strict deadline.

| | Hard Real-Time | Soft Real-Time |
|--|---------------|---------------|
| Deadline | Must never be missed — failure is catastrophic | Occasional misses are tolerable |
| Example | Anti-lock braking systems, pacemakers, airbags | Video streaming, online gaming |
| Memory | Often no virtual memory (too unpredictable) | Virtual memory typically OK |
| OS | Specialized RTOS (FreeRTOS, VxWorks) | General OS with RT extensions |

### Mobile / Handheld Systems

- Constrained resources: limited battery, CPU, memory.
- Sensor-rich: GPS, accelerometer, camera.
- Always-connected: WiFi, cellular.
- OS examples: Android (Linux-based), iOS (XNU kernel).
- Key concerns: Power management, responsive UI, app sandboxing (security).

**Priority:** ⭐⭐⭐⭐

---

## 17. OS Structures & Architectures

*College Foundation + Placement Extension*

### Simple / Monolithic Structure ⭐⭐⭐⭐

**Idea:** The entire OS is a single large program running in kernel mode. All OS services (file system, memory management, scheduling, I/O) are in one big module with no clear separation.

**Example:** Early MS-DOS, original Unix.

```
User Program
    ↓
Kernel (everything in one layer)
    ↓
Hardware
```

**Advantages:**
- Fast — no overhead from inter-process communication between modules.
- Direct access between components.

**Disadvantages:**
- Hard to maintain, debug, and extend — change in one part can break others.
- A bug anywhere can crash the entire system.
- Not secure — all code runs with full privileges.

### Layered Structure ⭐⭐⭐

**Idea:** OS is divided into layers. Each layer uses services only from the layer below it. Hardware is layer 0; user programs are the top layer.

```
Layer N   (User Programs)
Layer N-1 (I/O management)
  ...
Layer 1   (Memory management)
Layer 0   (Hardware)
```

**Advantages:**
- Clean abstraction; easy to debug and verify (test each layer independently).
- Modularity.

**Disadvantages:**
- Performance — each operation must pass through multiple layers (overhead).
- Difficult to define clean layer boundaries in practice.

### Microkernel ⭐⭐⭐⭐

**Idea:** Minimize what runs in the kernel. Only the most essential functions stay in the kernel: process scheduling, basic memory management, inter-process communication (IPC). Everything else (file system, device drivers, networking) runs in **user space** as separate server processes.

```
User:  [File Server] [Device Driver] [Network Server] [App]
                  ↕ IPC (message passing) ↕
Kernel: [Scheduling] [Basic Memory] [IPC]
Hardware
```

**Advantages:**
- More secure — services in user space; kernel crash is isolated.
- More reliable — services can be restarted without rebooting.
- Easier to port to new hardware.

**Disadvantages:**
- **Performance** — user-space services communicate via IPC (message passing) → overhead. A file operation requires multiple user↔kernel switches.

**Examples:** Mach, L4, QNX, macOS (uses Mach microkernel), MINIX.

### Modular / Loadable Kernel Modules (LKM) ⭐⭐⭐⭐

**Idea:** Monolithic kernel with modules that can be **dynamically loaded and unloaded** at runtime. Core kernel is lean; modules (device drivers, file systems) are loaded only when needed.

**Example:** Linux uses loadable kernel modules. `lsmod`, `insmod`, `rmmod` commands.

**Best of both worlds:** Performance of monolithic (modules run in kernel space) + flexibility of microkernels (modules are separate).

### Comparison

| Architecture | Performance | Reliability | Extensibility | Example |
|-------------|------------|------------|--------------|---------|
| Monolithic | Excellent | Lower | Hard | Early Unix, MS-DOS |
| Layered | Good | Medium | Moderate | THE system |
| Microkernel | Lower (IPC overhead) | High | High | MINIX, QNX |
| Modular | Excellent | Good | High | Linux, Solaris |

**Interview Angle:** "Why is Linux not a microkernel?" → Linux is a monolithic kernel (with modules). Linus Torvalds and Andy Tanenbaum had a famous debate about this. Monolithic Linux is faster; Tanenbaum preferred microkernels for reliability.

**Priority:** ⭐⭐⭐⭐

---

## 18. Important OS Concept Connections

*These connections are what separate someone who has memorized OS from someone who truly understands it.*

### Connection 1 — The Application-to-Hardware Chain

```
Application
    │  calls API (e.g., fopen, write)
    ↓
Library (API)
    │  issues trap instruction
    ↓
[MODE SWITCH: User → Kernel]
    ↓
Kernel (System Call Handler)
    │  invokes appropriate subsystem
    ↓
Device Driver (if I/O needed)
    │  sends command to controller
    ↓
Device Controller
    │  operates physical device
    ↓
I/O Device
    │  completes operation, signals interrupt
    ↓
CPU handles interrupt → Kernel → returns to User Mode
```

This is the complete path for a single `read()` call from a user program.

---

### Connection 2 — Process Lifecycle and the CPU Scheduler

```
Program (on disk)
    │ OS loads it
    ↓
Process (New state)
    │ admitted to memory
    ↓
Ready Queue  ←──────────────────────────────┐
    │ CPU Scheduler picks next process       │
    ↓                                       │
Running (CPU executing)                     │ I/O completes
    │                                       │
    ├─ Time quantum expires ────────────────→ Ready Queue
    ├─ I/O request ────────────────────────→ Waiting Queue
    │                                               │
    │                                        I/O completes
    │                                               │
    │                                        Ready Queue ──────────┘
    │
    └─ Process exits ───────────────────────→ Terminated
            │
        OS cleans up PCB and resources
```

---

### Connection 3 — Shared Resources, Race Conditions, and Deadlock

```
Multiple Processes/Threads
    │ access
    ↓
Shared Resource (variable, file, device)
    │ without coordination →
    ↓
Race Condition (non-deterministic wrong output)
    │ solution:
    ↓
Synchronization (Mutex / Semaphore)
    │ but if applied poorly →
    ↓
Deadlock (P1 holds R1 waits R2; P2 holds R2 waits R1)
    │ needs:
    ↓
Deadlock Handling (Prevention / Avoidance / Detection)
```

---

### Connection 4 — Memory to Thrashing

```
Process needs a page
    │
    ↓
Check Page Table
    │
    ├── Valid (in RAM) → Physical Address via MMU → Access data
    │
    └── Invalid → Page Fault
            │
            ↓
        Check TLB first (fast cache of page table)
            │
            ├── TLB Hit → Physical Address (fast!)
            │
            └── TLB Miss → Access page table in RAM
                    │
                    ├── Page in RAM → Update TLB → Access data
                    │
                    └── Page on Disk → Page Fault Handler
                            │
                            ↓
                        Find/evict a victim page (replacement algorithm)
                            │
                            ↓
                        Load page from disk into RAM frame
                            │
                            ↓
                        Update page table + TLB
                            │
                            ↓
                        Restart instruction
                            │
                        [If too many page faults → Thrashing]
```

---

### Connection 5 — I/O and the Interrupt Path

```
Application calls write() to disk
    │ system call
    ↓
Kernel → Device Driver → Controller: "Write this block"
    │
    ↓
Controller operates disk (mechanical / flash)
    │
CPU is free to run other processes meanwhile
    │
Controller finishes → sends Interrupt to CPU
    │
CPU (between instructions): interrupt detected
    │
Save current process state → jump to ISR
    │
ISR: read data from controller buffer, mark I/O complete
    │
Restore process state / wake waiting process
    │
Process resumes
```

---

### Connection 6 — OS Type to Scheduling

```
Batch System → maximize throughput → FCFS / SJF
Time-Sharing → minimize response time → Round Robin
Real-Time → meet deadlines → Priority / EDF (Earliest Deadline First)
Multiprogramming → maximize CPU utilization → any non-idle algorithm
```

---

## 19. High-Yield Comparisons

*All comparisons in one place — ideal for revision before interviews.*

### OS vs Kernel

| | OS | Kernel |
|--|----|----|
| Scope | Entire system software including utilities, shells, libraries | Core program that runs at all times |
| In memory | Partially loaded as needed | Always in memory |
| Example | Windows 11 | NT Kernel |

### Program vs Process

| | Program | Process |
|--|---------|---------|
| Nature | Passive (file on disk) | Active (executing in memory) |
| State | Static | Dynamic |
| Resources | None (just bytes) | CPU, memory, files, I/O |
| Count | One | Can spawn multiple processes |

### Process vs Thread

| | Process | Thread |
|--|---------|--------|
| Address space | Own separate space | Shares process space |
| Resources | Own resources | Shares process resources |
| Creation | Expensive | Cheap |
| Communication | IPC (slow) | Shared memory (fast, needs sync) |
| Crash impact | Isolated | Can crash whole process |
| Context switch | Expensive | Cheaper |

### User Mode vs Kernel Mode

| | User Mode | Kernel Mode |
|--|-----------|-------------|
| Privilege | Restricted | Full |
| Hardware access | No | Yes |
| Privileged instructions | Cannot execute | Can execute |
| Who runs | User programs | OS kernel |
| Mode bit | 1 | 0 |

### API vs System Call

| | API | System Call |
|--|-----|-------------|
| Level | User space | Kernel space |
| Called by | Programmer | Library (on programmer's behalf) |
| Language | C, Python, etc. | Assembly (trap) |
| Example | `fopen()` | `open` (Linux syscall) |

### Interrupt vs Polling

| | Interrupt | Polling |
|--|-----------|---------|
| Who initiates | Device signals CPU | CPU repeatedly checks device |
| CPU usage | Efficient (CPU free until signal) | Wasteful (constant checking) |
| Response time | Low latency | Depends on poll frequency |

### Interrupt vs Trap

| | Hardware Interrupt | Trap (Software Interrupt) |
|--|------------------|--------------------------|
| Source | External device | Software (syscall or exception) |
| Intentional | Device-driven | Syscall: yes; Exception: no |
| Example | Keyboard press | `read()` syscall, divide-by-zero |

### Device Driver vs Device Controller

| | Device Driver | Device Controller |
|--|--------------|-------------------|
| Type | Software | Hardware |
| Location | OS kernel | Inside/on the device |
| Role | Translates OS commands | Executes them electrically |

### Buffering vs Caching vs Spooling

| | Buffering | Caching | Spooling |
|--|-----------|---------|---------|
| Purpose | Handle speed mismatch during transfer | Store frequently accessed data for fast reuse | Queue jobs for a device |
| Where | RAM | RAM / Cache | Disk |
| Example | Network receive buffer | Disk block cache in RAM | Printer queue |

### Without DMA vs With DMA

| | Without DMA | With DMA |
|--|------------|---------|
| Who moves data | CPU (byte by byte) | DMA controller |
| CPU during transfer | Busy (copying data) | Free (doing other work) |
| Interrupt | Per byte | Once at end of transfer |

### Multiprogramming vs Multitasking

| | Multiprogramming | Multitasking / Time-Sharing |
|--|-----------------|---------------------------|
| Goal | CPU utilization | User response time |
| Switching | On I/O (voluntary) | Time-driven (preemptive) |
| Users | Typically batch jobs | Multiple interactive users |

### Parallel vs Distributed Systems

| | Parallel (Multiprocessing) | Distributed |
|--|--------------------------|------------|
| Memory | Shared | Separate |
| Location | One machine | Multiple machines |
| Communication | Shared memory | Network |

### Hard vs Soft Real-Time

| | Hard Real-Time | Soft Real-Time |
|--|---------------|---------------|
| Deadline miss | Catastrophic | Degraded quality |
| Example | Pacemaker, airbag | Video streaming |
| Virtual memory | Often avoided | OK |

### Mutex vs Semaphore

| | Mutex | Semaphore |
|--|-------|-----------|
| Ownership | Yes (locker must unlock) | No |
| Value | Binary | Any non-negative integer |
| Purpose | Mutual exclusion | Signaling / resource counting |
| Signaling thread | Same as locking thread | Any thread |

### Deadlock vs Starvation

| | Deadlock | Starvation |
|--|---------|-----------|
| Cause | Circular wait for resources | Scheduling (never gets CPU) |
| Blocked by | Each other | Higher-priority processes |
| Solution | Prevention/avoidance/detection | Aging |

### Prevention vs Avoidance vs Detection

| | Prevention | Avoidance | Detection |
|--|-----------|-----------|----------|
| When | Before deadlock | Before each allocation | After deadlock occurs |
| Method | Eliminate a Coffman condition | Banker's algorithm (safe state check) | RAG / detection algorithm |
| Resource use | Conservative (under-utilized) | Moderate | Optimal (allows deadlock) |
| Overhead | Low (policy) | Medium (check per request) | Periodic algorithm cost |

### Internal vs External Fragmentation

| | Internal Fragmentation | External Fragmentation |
|--|----------------------|----------------------|
| Wasted space | Inside allocated block | Between allocated blocks |
| Cause | Fixed partitions / pages | Variable allocation |
| Solution | Smaller page size | Compaction, paging |

### Paging vs Segmentation

| | Paging | Segmentation |
|--|--------|-------------|
| Unit size | Fixed (pages) | Variable (segments) |
| Based on | Physical convenience | Logical structure |
| Internal frag | Yes | No |
| External frag | No | Yes |
| Programmer view | Transparent | Visible |

### Page Fault vs TLB Miss

| | TLB Miss | Page Fault |
|--|---------|-----------|
| Page in RAM? | Yes | No |
| Disk access? | No | Yes |
| Cost | Moderate (RAM access) | High (disk access) |
| Handled by | Hardware (MMU) / OS | OS page fault handler |

### Logical vs Physical Address

| | Logical Address | Physical Address |
|--|----------------|-----------------|
| Generated by | CPU | MMU translates to this |
| Seen by | Program | RAM hardware |
| Also called | Virtual address | Real address |

### File Allocation: Contiguous vs Linked vs Indexed

| | Contiguous | Linked | Indexed |
|--|-----------|--------|---------|
| Sequential access | Fast | OK | Fast |
| Random access | Fast | Slow | Fast |
| External frag | Yes | No | No |
| File growth | Hard | Easy | Easy |
| Overhead | None | Per-block pointer | Index block |

---

## 20. Common Interview Questions

### OS Fundamentals

**Q: What is an operating system?**
> A program that acts as an intermediary between users and hardware. It manages hardware resources (CPU, memory, I/O) and provides an environment where programs can run efficiently and safely.

**Q: What are the main functions of an OS?**
> Process management, memory management, file system management, I/O management, security and protection, resource allocation.

**Q: What is a kernel?**
> The core part of the OS that runs at all times. It directly interacts with hardware and provides fundamental services: scheduling, memory management, and IPC.

**Q: Kernel vs OS?**
> The kernel is the core of the OS. OS = Kernel + System Programs + Utilities. The kernel is always in memory; other parts are loaded when needed.

**Q: What is a bootstrap program?**
> The first program that runs when a computer powers on (BIOS/UEFI). It initializes hardware and loads the OS kernel into memory.

---

### System Calls

**Q: What is a system call?**
> A system call is the programmatic way for a user-level process to request a service from the OS kernel. It is the controlled entry point into the kernel.

**Q: API vs system call?**
> An API is a library function (e.g., `fopen()` in C) that programmers use. The API internally invokes the actual system call. API is user-level; system call crosses into kernel mode.

**Q: What happens during a system call?**
> 1. User program executes a trap instruction. 2. CPU switches to kernel mode. 3. Kernel handles the request. 4. Kernel returns result. 5. CPU switches back to user mode. 6. Program resumes.

**Q: Why can't applications directly access hardware?**
> Security and stability. If any program could access hardware directly, a buggy or malicious program could corrupt memory, crash the system, or access other programs' data. The OS enforces controlled access via system calls and the mode bit.

**Q: What are privileged instructions?**
> Instructions that can only execute in kernel mode (e.g., halt CPU, control I/O devices, modify memory management registers). If a user program tries to execute one, the hardware generates a trap and the OS terminates the program.

---

### Processes

**Q: Program vs process?**
> A program is a passive entity — code stored on disk. A process is a program in execution — an active entity with resources (CPU, memory, I/O).

**Q: What is a PCB?**
> Process Control Block — the OS data structure that represents a process. Contains PID, state, program counter, registers, memory maps, open files, and scheduling information.

**Q: What is context switching?**
> Saving the state of the current running process into its PCB and loading the state of the next process from its PCB. Pure overhead — no useful computation happens during a context switch.

**Q: What are the process states?**
> New → Ready → Running → Waiting/Blocked → Terminated. A process moves between these states based on scheduling decisions and I/O events.

**Q: What is the difference between zombie and orphan processes?**
> Zombie: child has exited but parent hasn't called `wait()` — PCB remains. Orphan: parent exits before child — adopted by init (PID 1).

---

### Threads

**Q: Process vs thread?**
> A process is an independent execution environment with its own address space. A thread is an execution unit within a process, sharing the process's address space, code, data, and open files — but with its own stack, registers, and program counter.

**Q: Why use threads instead of processes?**
> Threads are cheaper to create, share memory directly (fast communication), and have cheaper context switches. Ideal for parallel tasks within one application.

**Q: User-level vs kernel-level threads?**
> User-level threads are managed by a library; the kernel sees one process. Kernel-level threads are managed by the OS — true parallelism, but more overhead per thread.

**Q: What is the many-to-one threading model?**
> Many user threads map to one kernel thread. Simple but no parallelism — if one thread blocks, all block.

---

### CPU Scheduling

**Q: Preemptive vs non-preemptive scheduling?**
> Preemptive: OS can forcibly take CPU from a running process. Non-preemptive: process keeps CPU until it finishes or blocks voluntarily.

**Q: What is the convoy effect in FCFS?**
> A long CPU-bound process runs while many short processes queue behind it, increasing average waiting time significantly. Like a slow truck blocking a line of fast cars.

**Q: Why is SJF optimal?**
> SJF minimizes average waiting time. It gives the CPU to shorter jobs first, reducing the total accumulated wait for all jobs. Proven mathematically for non-preemptive scheduling.

**Q: Why use Round Robin?**
> Ensures every process gets a fair share of CPU time. Ideal for interactive/time-sharing systems — short response time for all processes.

**Q: What is starvation? How is it solved?**
> Starvation: a low-priority process never gets the CPU because high-priority processes keep arriving. Solved by **aging** — gradually increase the priority of waiting processes.

**Q: What is MLFQ?**
> Multi-Level Feedback Queue. Multiple ready queues with different priorities and time quanta. Processes move between queues based on CPU behavior — I/O-bound processes stay high; CPU-bound settle lower.

---

### Synchronization

**Q: What is a race condition?**
> When two or more processes access shared data concurrently and the outcome depends on the order of execution. Results are non-deterministic and often incorrect.

**Q: What are the three requirements for the critical section problem?**
> 1. Mutual Exclusion — only one process in CS at a time. 2. Progress — if CS is free and processes want to enter, selection cannot be postponed. 3. Bounded Waiting — no process waits indefinitely.

**Q: Mutex vs semaphore?**
> Mutex: binary lock with ownership — only the locker can unlock. Semaphore: integer with wait/signal — any thread can signal; supports counting (N resources).

**Q: What is a binary semaphore vs counting semaphore?**
> Binary semaphore has value 0 or 1 — behaves like a mutex. Counting semaphore has value 0 to N — controls access to a pool of N identical resources.

**Q: What is a spinlock?**
> A mutex implemented with busy waiting — thread loops (spins) until the lock is free. Efficient only when the wait time is very short and we're on a multi-core system.

---

### Deadlocks

**Q: What are the four Coffman conditions?**
> 1. Mutual Exclusion, 2. Hold and Wait, 3. No Preemption, 4. Circular Wait. All four must hold simultaneously for deadlock to occur.

**Q: Deadlock vs starvation?**
> Deadlock: processes wait for each other's resources — circular, infinite wait. Starvation: process waits for CPU due to scheduling — others run, it doesn't. Starvation has a solution (aging); deadlock requires special handling.

**Q: Prevention vs avoidance?**
> Prevention eliminates one Coffman condition by policy (e.g., request all resources at once). Avoidance allows all conditions but uses algorithms (Banker's) to ensure the system never enters an unsafe state.

**Q: What is a safe state?**
> A state in which there exists a safe sequence — an ordering of processes such that each can eventually complete using currently available + eventually released resources. Safe state guarantees no deadlock.

**Q: Explain Banker's Algorithm.**
> Before granting a resource, simulate the allocation and check if the resulting state is safe (a safe sequence exists). If safe → grant. If unsafe → make process wait. Requires knowing processes' maximum resource needs in advance.

---

### Memory Management

**Q: Paging vs segmentation?**
> Paging: fixed-size pages, no external fragmentation, transparent to programmer. Segmentation: variable-size logical segments, no internal fragmentation, visible to programmer.

**Q: Internal vs external fragmentation?**
> Internal: wasted space inside an allocated block (block bigger than needed). External: total free memory sufficient but not contiguous enough for a large request.

**Q: Logical vs physical address?**
> Logical (virtual) address is generated by the CPU and seen by the program. Physical address is the actual RAM location. The MMU translates between them at runtime.

---

### Virtual Memory

**Q: What is virtual memory?**
> A technique that gives each process the illusion of a large, contiguous address space, even if physical RAM is smaller. Uses disk as extension of RAM, loading pages on demand.

**Q: What is a page fault?**
> When a process accesses a page not currently in RAM (valid bit = 0). OS handles it: find page on disk, load into a free frame, update page table, restart the instruction.

**Q: What is TLB?**
> Translation Lookaside Buffer — a fast hardware cache for page table entries. Stores recent page-to-frame mappings. A TLB hit avoids a RAM access for page table lookup.

**Q: What is thrashing?**
> When a process has fewer frames than its working set, it constantly page faults. OS spends more time paging than running processes. CPU utilization collapses.

**Q: FIFO vs LRU page replacement?**
> FIFO replaces the oldest page — simple but suffers Belady's Anomaly. LRU replaces the least recently used page — better approximates optimal, no Belady's Anomaly, but more expensive to implement.

---

### File Systems

**Q: What is a file system?**
> The OS component that manages how data is stored and retrieved on storage devices. Provides abstraction (files and directories) over raw disk blocks.

**Q: What are the file allocation methods?**
> Contiguous (fast access, external frag), Linked (flexible, slow random access), Indexed (fast random access, index block overhead — used in inode).

**Q: What is an inode?**
> A Unix data structure storing file metadata and pointers to data blocks. Contains direct, indirect, double-indirect, triple-indirect block pointers. Does NOT store the filename.

**Q: Hard link vs soft link?**
> Hard link: another directory entry pointing to the same inode. Soft link: a file containing the path to another file. Hard links survive source deletion; soft links become dangling.

---

### I/O

**Q: What is an interrupt?**
> A signal from a device or software to the CPU to suspend current execution and handle an event. The CPU saves state, jumps to the ISR, handles the event, then resumes.

**Q: What is DMA?**
> Direct Memory Access — allows a device to transfer data directly to/from RAM without CPU involvement. CPU only sets up the transfer and is interrupted when complete. Frees CPU for other work.

**Q: Device driver vs controller?**
> Device driver: OS software that translates requests into device commands. Device controller: hardware that electrically operates the device. Driver talks to controller; controller talks to device.

**Q: What is spooling?**
> Simultaneous Peripheral Operations On-Line. Queuing jobs on disk for a device that cannot handle concurrent requests (e.g., printer spool). Allows programs to "print" without waiting for the actual printer.

---

### OS Structures

**Q: Monolithic vs microkernel?**
> Monolithic: entire OS in one kernel-mode program — fast but fragile. Microkernel: only essential services in kernel; rest in user space — reliable and secure but slower (IPC overhead).

**Q: Why use microkernels?**
> Fault isolation — a service crash doesn't bring down the kernel. Easier to extend. More secure — less code runs in privileged mode.

**Q: What is a loadable kernel module?**
> A piece of kernel code that can be dynamically loaded and unloaded at runtime. Used in Linux for device drivers and file systems. Combines monolithic performance with modular flexibility.

---

## 21. Interview "Why" Questions

*Understanding the "why" is what separates surface knowledge from genuine understanding.*

**Q: Why can't user programs directly access hardware?**
> If any program could touch hardware directly, a bug or malicious code could corrupt other processes' memory, crash the OS, or access protected data. The OS enforces controlled access through system calls and mode switching, ensuring safety and isolation.

**Q: Why are I/O instructions privileged?**
> I/O devices are shared resources. Direct access by user programs would allow one process to monopolize a device, read another process's I/O data, or corrupt device state. The OS mediates all I/O to ensure correct sharing.

**Q: Why do we need system calls?**
> To provide a safe, controlled interface between user programs and the OS kernel. Without system calls, user programs would need to either access hardware directly (unsafe) or have OS code in user space (insecure). System calls enforce the privilege boundary.

**Q: Why do we need interrupts?**
> Without interrupts, the CPU would have to constantly poll every device to check if it needs attention — wasting CPU cycles. Interrupts let devices notify the CPU only when they need service, allowing the CPU to do useful work in between.

**Q: Why is DMA useful?**
> For large data transfers, having the CPU copy every byte from a device to memory is extremely wasteful. DMA offloads this to a dedicated controller, freeing the CPU to run processes while data is being transferred. This dramatically improves throughput.

**Q: Why do we need CPU scheduling?**
> Processes frequently wait for I/O. Without scheduling, the CPU would sit idle while a process waits. Scheduling ensures the CPU is always working on something productive, maximizing utilization and system throughput.

**Q: Why is context switching expensive?**
> The OS must save all CPU registers, program counter, and memory state of the current process, then load the full state of the next process. For processes, the page table (memory map) must also switch — which may invalidate the TLB. None of this does useful computation.

**Q: Why do we need synchronization?**
> When multiple threads share memory and run concurrently, a context switch mid-operation (e.g., between read and write of a shared variable) can leave data in an inconsistent state. Synchronization ensures such operations complete atomically from the perspective of other threads.

**Q: Why can deadlock occur?**
> Because processes hold resources while waiting for more, and no one preempts. If processes form a circular dependency (each waiting for a resource held by the next), no one can proceed — classic deadlock. All four Coffman conditions must hold simultaneously.

**Q: Why do we need virtual memory?**
> Real programs are often larger than physical RAM. Virtual memory lets programs run as if they have more memory than exists, by keeping only the active pages in RAM and storing the rest on disk. It also provides isolation (each process sees its own virtual address space).

**Q: Why is TLB needed?**
> Paging requires two memory accesses per data access: one for the page table, one for the actual data. This doubles access time. The TLB caches recent page-to-frame mappings, so most accesses need only one memory access — critical for performance.

**Q: Why does thrashing occur?**
> When the OS keeps too many processes in memory simultaneously, each process gets too few frames to hold its working set. Every access causes a page fault, and the OS spends all its time swapping pages in and out from disk instead of executing processes.

**Q: Why do we need file systems?**
> Raw storage (disk) is just blocks of bits. A file system provides structure: named files, directories, permissions, and efficient access. Without it, every program would need to manage raw disk blocks itself — error-prone and impractical.

**Q: Why do we need protection?**
> Multiple users and processes share a computer. Without protection, a bug in one process could corrupt another's memory, a malicious user could read another's files, or a runaway process could monopolize CPU or memory. Protection mechanisms (mode bit, memory bounds, permissions) keep the system safe and stable.

---

## 22. Common Confusions & Mistakes

**OS ≠ Kernel**
The OS is the complete system (kernel + system programs + utilities). The kernel is the always-running core. Saying "Linux is an OS" is common but technically Linux is the kernel; GNU/Linux is the OS.

**Program ≠ Process**
A program is a static file on disk. A process is a running instance of a program with resources. One program can create many processes. Many different programs can be running as processes simultaneously.

**Process ≠ Thread**
A process is an independent execution environment with its own address space. A thread is an execution unit *within* a process, sharing its address space. A process has at least one thread. Do not use these interchangeably.

**API ≠ System Call**
`fopen()` is an API call. The `open` system call is what `fopen()` internally invokes. The API is the high-level interface for programmers; the system call is the actual mechanism crossing into kernel mode.

**Interrupt ≠ Trap**
An interrupt is an asynchronous signal from external hardware (device). A trap is a synchronous software-generated signal (intentional system call or unintentional exception). Both transfer control to the kernel, but from different sources.

**Interrupt ≠ Polling**
Interrupts are event-driven — device notifies CPU. Polling is CPU-driven — CPU repeatedly checks. These are opposite strategies for handling device readiness.

**Mutex ≠ Semaphore**
A mutex has ownership — only the thread that locked it can unlock it. A semaphore has no ownership — any thread can signal it. A binary semaphore looks like a mutex but is not — it can be signaled by a different thread.

**Deadlock ≠ Starvation**
In deadlock, processes are stuck *waiting for each other's resources* — no one runs. In starvation, the process is alive and keeps trying but the scheduler never picks it — others keep running. Deadlock is a system-wide circular block; starvation is a scheduling unfairness.

**Page Fault ≠ TLB Miss**
A TLB miss means the TLB does not have the page-to-frame mapping — but the page IS in RAM. The MMU then looks up the page table (just one extra RAM access). A page fault means the page is NOT in RAM at all — requires disk access (thousands of times slower).

**Paging ≠ Virtual Memory**
Paging is a memory allocation mechanism (fixed-size pages/frames). Virtual memory is the concept of using disk to extend RAM. Virtual memory is typically *implemented using* paging (demand paging), but they are not the same thing.

**Protection ≠ Security**
Protection = internal OS mechanisms preventing processes from accessing unauthorized resources. Security = broader — authentication, encryption, network defense against external threats.

**Device Driver ≠ Device Controller**
Driver is software in the OS kernel. Controller is hardware on the motherboard or in the device. The driver sends commands to the controller; the controller operates the physical device.

**Multiprogramming ≠ Multiprocessing**
Multiprogramming = multiple programs in memory, one CPU, maximizes utilization by switching when one waits. Multiprocessing = multiple CPUs, true parallel execution.

**Context Switch ≠ Process Creation**
Context switch: save current process state, load another's state. No new process is created. Process creation (fork): creates a new PCB, allocates memory, etc. — much more expensive.

**Logical Address ≠ Physical Address**
Logical (virtual) address is what the program sees. Physical address is the actual RAM location. They differ because the MMU applies the page table mapping. Never assume they are equal.

---

## 23. Quick Revision Sheet

*Last-minute prep before an exam or interview.*

### Important Definitions

| Term | One-line Definition |
|------|-------------------|
| OS | Intermediary between users and hardware; manages resources |
| Kernel | Always-running core of the OS; directly controls hardware |
| System Call | Controlled entry from user mode into kernel mode for OS service |
| Mode Bit | CPU bit: 0=kernel mode, 1=user mode |
| PCB | OS data structure representing a process (state, registers, memory info) |
| Context Switch | Save current process state → load next process state |
| Mutex | Ownership-based binary lock for mutual exclusion |
| Semaphore | Counter-based signaling mechanism; wait()/signal() |
| Deadlock | Circular wait for resources; no process can proceed |
| Page Fault | Access to a page not in RAM; OS loads it from disk |
| TLB | Fast hardware cache for page table entries |
| Thrashing | OS spends more time paging than executing; CPU utilization collapses |
| DMA | Device transfers data directly to RAM; CPU not involved per-byte |
| Spooling | Queuing jobs for a slow device (e.g., printer) |
| Inode | Unix data structure storing file metadata and block pointers |

### Important Formulas

```
Turnaround Time (TAT) = Completion Time - Arrival Time
Waiting Time (WT)     = TAT - Burst Time
Response Time         = First CPU Time - Arrival Time

Effective Access Time (with TLB):
  EAT = h*(t_tlb + t_mem) + (1-h)*(t_tlb + 2*t_mem)
  where h = TLB hit ratio, t_mem = memory access time

Need (Banker's) = Max - Allocation
```

### Coffman Conditions (All 4 must hold for deadlock)
1. Mutual Exclusion
2. Hold and Wait
3. No Preemption
4. Circular Wait

→ **Trick: MHNC** (My Horse Never Circles)

### Critical Section Requirements
1. Mutual Exclusion
2. Progress
3. Bounded Waiting

### Page Replacement — Key Points
- **FIFO** — oldest page evicted; can suffer Belady's Anomaly
- **LRU** — least recently used; best practical; no Belady's
- **Optimal** — replace farthest future use; not implementable; benchmark only
- **Clock** — FIFO + reference bit; good practical performance

### Scheduling Summary
| Algorithm | Preemptive | Best For |
|-----------|-----------|---------|
| FCFS | No | Batch, simple |
| SJF | No | Min avg WT (theory) |
| SRTF | Yes | Min avg WT (preemptive) |
| Round Robin | Yes | Interactive, time-sharing |
| Priority | Both | Priority-based systems |
| MLFQ | Yes | General-purpose OS |

### OS Architecture Summary
| Type | Key Idea | Example |
|------|---------|---------|
| Monolithic | All in kernel | Early Unix |
| Layered | Hierarchical layers | THE |
| Microkernel | Minimal kernel, services in user space | MINIX, QNX |
| Modular | Monolithic + dynamic modules | Linux |

### Important Interview One-Liners
- "A process is an isolated execution environment; a thread is an execution unit within it sharing the process's address space."
- "Context switching is pure overhead — no computation happens; that's why we minimize it."
- "Deadlock requires all four Coffman conditions simultaneously — eliminate one to prevent it."
- "TLB is to page tables what cache is to RAM — a small, fast lookup store."
- "Thrashing is the OS punishing itself for being too greedy with processes."
- "DMA frees the CPU from moving data byte-by-byte — it just sets up the transfer and gets interrupted when done."
- "A system call is how a user program politely asks the OS kernel to do something privileged on its behalf."
- "The mode bit is the hardware enforcement of the user/kernel boundary."
- "Aging solves starvation by making sure no process waits forever — priority increases with wait time."
- "Banker's algorithm is safe but impractical — it requires knowing maximum resource needs in advance."

---

## 24. OS Interview Checklist

Use this before interviews to verify you are ready.

### Fundamentals
- [ ] OS definition and goals
- [ ] OS as Resource Allocator + Control Program
- [ ] Kernel vs OS
- [ ] System programs vs kernel
- [ ] Bootstrap process (BIOS → kernel)
- [ ] User Mode and Kernel Mode (mode bit)
- [ ] Privileged instructions

### System Calls
- [ ] What is a system call and why needed
- [ ] API vs system call
- [ ] System call flow (trap → kernel → return)
- [ ] Categories of system calls
- [ ] Why applications cannot access hardware directly

### Interrupts & I/O
- [ ] Hardware interrupt vs software trap
- [ ] Interrupt vector and ISR
- [ ] Polling vs interrupt
- [ ] Device driver vs device controller
- [ ] Buffering vs caching vs spooling
- [ ] DMA — what, why, and how it works
- [ ] Interrupt-driven I/O flow

### Processes
- [ ] Program vs process
- [ ] Process memory layout (text, data, heap, stack)
- [ ] Process Control Block (PCB) contents
- [ ] Process states and all transitions
- [ ] Context switch — steps and overhead
- [ ] Long-term vs short-term vs medium-term scheduler
- [ ] fork() / exec() / wait() / exit()
- [ ] Zombie vs orphan process

### Threads
- [ ] Process vs thread differences
- [ ] Benefits of multithreading
- [ ] User-level vs kernel-level threads
- [ ] Many-to-one, one-to-one, many-to-many models
- [ ] When threads are better than processes

### CPU Scheduling
- [ ] Scheduling criteria (CPU util, throughput, TAT, WT, RT)
- [ ] Preemptive vs non-preemptive
- [ ] FCFS — convoy effect
- [ ] SJF — optimal, starvation
- [ ] SRTF — preemptive SJF
- [ ] Round Robin — quantum size effect
- [ ] Priority scheduling — starvation, aging
- [ ] MLFQ — structure and behavior

### Synchronization
- [ ] Race condition — what and why
- [ ] Critical section — 3 requirements
- [ ] Mutex — ownership, usage
- [ ] Semaphore — wait/signal, binary vs counting
- [ ] Mutex vs semaphore differences
- [ ] Spinlock and busy waiting
- [ ] Producer-Consumer problem and solution
- [ ] Reader-Writer problem
- [ ] Dining Philosophers — deadlock scenario

### Deadlocks
- [ ] Deadlock definition
- [ ] Four Coffman conditions
- [ ] Resource Allocation Graph (RAG) — cycle meaning
- [ ] Prevention — how to eliminate each condition
- [ ] Avoidance — safe state concept
- [ ] Banker's Algorithm — how it works
- [ ] Detection — algorithm for multi-instance
- [ ] Recovery — process termination vs resource preemption
- [ ] Deadlock vs starvation vs livelock

### Memory Management
- [ ] Logical vs physical address
- [ ] MMU role
- [ ] Address binding — compile/load/execution time
- [ ] Contiguous allocation — fixed vs variable partitioning
- [ ] Internal vs external fragmentation
- [ ] Paging — pages, frames, page table
- [ ] Address translation in paging
- [ ] Multi-level page tables
- [ ] Segmentation — segments, base/limit
- [ ] Paging vs segmentation comparison

### Virtual Memory
- [ ] Virtual memory concept and purpose
- [ ] Demand paging and valid/invalid bit
- [ ] Page fault — what triggers it, handling steps
- [ ] TLB — hit, miss, EAT formula
- [ ] Page replacement — FIFO, LRU, Optimal, Clock
- [ ] Belady's Anomaly — which algorithms suffer
- [ ] Thrashing — cause, detection, solutions
- [ ] Working Set model

### File Systems
- [ ] File attributes and operations
- [ ] Directory structures
- [ ] Contiguous allocation — pros/cons
- [ ] Linked allocation — FAT
- [ ] Indexed allocation — inode structure
- [ ] Hard link vs soft (symbolic) link
- [ ] Free space management (bitmap)

### Disk Scheduling
- [ ] Disk structure (tracks, sectors)
- [ ] Access time components (seek, rotation, transfer)
- [ ] FCFS, SSTF, SCAN, C-SCAN, LOOK, C-LOOK
- [ ] Which algorithm minimizes seek time

### OS Types
- [ ] Batch, Multiprogramming, Multitasking
- [ ] Multiprocessing — SMP
- [ ] Distributed systems
- [ ] Hard vs Soft Real-Time
- [ ] Multiprogramming vs Multitasking vs Multiprocessing

### OS Structures
- [ ] Monolithic — pros/cons
- [ ] Layered — pros/cons
- [ ] Microkernel — pros/cons, examples
- [ ] Modular (LKM) — Linux example

---

*End of OS Master Notes*

> **Study tip:** Go through sections 1–17 to build understanding. Use sections 18–22 to connect and sharpen concepts. Use sections 23–24 for final revision before an exam or interview.
