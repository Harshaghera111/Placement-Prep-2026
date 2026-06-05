# 💻 OS — Operating Systems

> OS concepts are fundamental for system-level interviews. Focus on processes, threads, memory management, and deadlocks.

---

## 📖 Key Topics Overview

| Topic | Interview Importance |
|-------|---------------------|
| Process vs Thread | ⭐⭐⭐⭐⭐ |
| Scheduling Algorithms | ⭐⭐⭐⭐ |
| Deadlock (4 conditions) | ⭐⭐⭐⭐⭐ |
| Memory Management | ⭐⭐⭐⭐ |
| Paging vs Segmentation | ⭐⭐⭐ |
| Virtual Memory | ⭐⭐⭐⭐ |
| Semaphore & Mutex | ⭐⭐⭐⭐ |

---

## 1. Process vs Thread

| Feature | Process | Thread |
|---------|---------|--------|
| Definition | Program in execution | Lightweight sub-unit of a process |
| Memory | Separate memory space | Shared memory within process |
| Communication | IPC (pipes, sockets, shared memory) | Direct (shared heap) |
| Creation time | Slow (heavy) | Fast (lightweight) |
| Crash impact | Isolated — one crash doesn't affect others | One crashed thread can crash the process |
| Context switch | Expensive | Cheaper |
| Example | Chrome tabs (each is a process) | Multiple tabs loading simultaneously (threads) |

**Interview one-liner:** "A process is an isolated execution environment. A thread is an execution unit within a process, sharing the process's memory and resources."

---

## 2. Process States

```
New → Ready → Running → Terminated
                ↓
            Waiting/Blocked (waiting for I/O)
                ↓
            Ready (I/O complete)
```

**States:**
- **New**: Process being created
- **Ready**: Waiting for CPU
- **Running**: Currently executing on CPU
- **Waiting/Blocked**: Waiting for I/O or event
- **Terminated**: Finished execution

---

## 3. CPU Scheduling Algorithms

| Algorithm | Description | Preemptive? | Issue |
|-----------|-------------|-------------|-------|
| **FCFS** | First come, first served | No | Convoy effect |
| **SJF** | Shortest job first | No | Starvation of long jobs |
| **SRTF** | Shortest Remaining Time First | Yes | Starvation |
| **Round Robin** | Time quantum for each process | Yes | High context switch if quantum too small |
| **Priority** | Higher priority → runs first | Both variants | Starvation (use aging) |
| **MLFQ** | Multiple queues by priority | Yes | Complex implementation |

**Key Metrics:**
- **Throughput**: Number of processes completed per unit time
- **Turnaround Time**: Time from submission to completion
- **Waiting Time**: Time spent in ready queue
- **Response Time**: Time from submission to first response

---

## 4. Deadlock

### 4 Necessary Conditions (Coffman Conditions)
**All four must hold for deadlock to occur:**

1. **Mutual Exclusion**: At least one resource is non-sharable
2. **Hold and Wait**: A process holds a resource and waits for more
3. **No Preemption**: Resources can't be forcibly taken away
4. **Circular Wait**: P1 waits for P2, P2 waits for P1 (cycle)

**Memory trick:** "MH-NP-CW" or "My Hold, Never Preempt, Circular Wait"

### Deadlock Handling Strategies

| Strategy | Approach |
|----------|---------|
| **Prevention** | Eliminate one of the 4 conditions (e.g., force all resources to be requested at once) |
| **Avoidance** | Use Banker's Algorithm to never enter unsafe state |
| **Detection** | Allow deadlock; detect using resource allocation graph; recover by killing processes |
| **Ignorance** | Ostrich Algorithm — reboot if deadlock (used in some OS) |

### Banker's Algorithm (Avoidance)
The OS checks if allocating a resource keeps the system in a "safe state" (a sequence exists where all processes can finish).

---

## 5. Memory Management

### Memory Allocation
- **Contiguous Allocation**: Each process gets a single continuous block
  - Fixed Partitioning (internal fragmentation)
  - Variable Partitioning (external fragmentation)
- **Non-Contiguous**: Pages/segments scattered in memory

### Fragmentation
- **Internal Fragmentation**: Allocated memory > required memory (wasted inside block)
- **External Fragmentation**: Enough total free memory, but not contiguous

### Paging
- Physical memory divided into fixed-size **frames**
- Logical memory divided into same-size **pages**
- **Page Table** maps page number → frame number
- **No external fragmentation** (may have internal)

### Segmentation
- Memory divided into variable-size **segments** (code, data, stack)
- Reflects programmer's view of memory
- **No internal fragmentation** (may have external)

| | Paging | Segmentation |
|--|--------|-------------|
| Size | Fixed | Variable |
| Internal Fragmentation | Yes | No |
| External Fragmentation | No | Yes |
| User Visibility | No (transparent) | Yes (logical segments) |

---

## 6. Virtual Memory

Virtual memory allows executing processes that are **larger than physical RAM** by using disk as an extension.

**Page Fault**: Accessing a page that's not in physical memory → OS loads it from disk

### Page Replacement Algorithms

| Algorithm | Description | Issue |
|-----------|-------------|-------|
| **FIFO** | Replace oldest page | Bélády's Anomaly |
| **LRU** | Replace least recently used | Implementation cost |
| **Optimal** | Replace page used farthest in future | Not feasible (needs future knowledge) |
| **LFU** | Replace least frequently used | Old pages with high frequency stay |
| **Clock (Second Chance)** | FIFO with reference bit | Good practical performance |

**Thrashing**: OS spends more time page swapping than executing processes (process has too few frames).

---

## 7. Synchronization

### Critical Section Problem
Requirements:
1. **Mutual Exclusion**: Only one process in critical section at a time
2. **Progress**: If no one is in critical section, waiting processes can enter
3. **Bounded Waiting**: A process must enter in finite time (no starvation)

### Mutex vs Semaphore

| Feature | Mutex | Semaphore |
|---------|-------|-----------|
| Type | Locking mechanism | Signaling mechanism |
| Owner | Yes — only owner can unlock | No ownership |
| Count | Binary (0/1) | Any non-negative integer |
| Use case | Mutual exclusion | Resource counting |
| Example | Prevent two threads writing same file | Limit to N database connections |

```python
import threading

mutex = threading.Lock()

# Mutex usage
with mutex:  # acquire on enter, release on exit
    # Critical section
    pass

# Semaphore usage
semaphore = threading.Semaphore(5)  # Allow 5 concurrent access
with semaphore:
    # Up to 5 threads can be here simultaneously
    pass
```

### Classic Synchronization Problems
- **Producer-Consumer**: Shared buffer; producer adds, consumer removes
- **Reader-Writer**: Multiple readers OR one writer at a time
- **Dining Philosophers**: 5 philosophers, 5 forks — classic deadlock scenario

---

## ❓ Frequently Asked Interview Questions

**Q1: Difference between process and thread?**
> A process is an independent program in execution with its own memory space. A thread is a lighter execution unit within a process, sharing the process's memory. Threads are faster to create and communicate but riskier (a crash affects all threads).

**Q2: What are the 4 conditions for deadlock?**
> Mutual Exclusion, Hold and Wait, No Preemption, Circular Wait. All 4 must hold simultaneously.

**Q3: What is a context switch?**
> Saving the state (registers, PC, stack pointer) of the current process and loading the state of the next process. It's overhead — that's why thread context switches are cheaper than process ones.

**Q4: Difference between mutex and semaphore?**
> Mutex is a locking mechanism where the same thread that locks must unlock — ensures mutual exclusion. Semaphore is a signaling mechanism with a counter — used for controlling access to a pool of resources.

**Q5: What is the difference between paging and segmentation?**
> Paging divides memory into fixed-size pages (transparent to programmer, no external fragmentation). Segmentation divides into variable-size logical segments (code, data, stack — visible to programmer, no internal fragmentation).

---

## ✅ Revision Checklist

- [ ] Can I explain process vs thread differences clearly?
- [ ] Can I name and explain the 4 deadlock conditions?
- [ ] Do I understand the 4 deadlock handling strategies?
- [ ] Can I explain FCFS, SJF, Round Robin scheduling?
- [ ] Do I understand paging vs segmentation?
- [ ] Can I explain virtual memory and page faults?
- [ ] Do I know the difference between mutex and semaphore?
- [ ] Can I explain the critical section problem requirements?
