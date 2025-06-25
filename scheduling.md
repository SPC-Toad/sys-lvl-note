# Welcome to scheduling!!!

## Multi-programming
- Execution of programs without any portion of their execution actually overlapping in time. Simply, multiple programs are executed on the same resource (like CPU core or thread)

# Queuing theory
- Assume that we have a client and server. The client(s) are sending requests to the server.

## Throughput 
- The number of completed "tasks" or "requests" divided by the total time. 
- This is also the service rate (Example: 4 requests per 1 ms)

## Bottleneck 
- The one component that has most utilization.

## Capacity
- Upper bound of the throughput.
- Question, isn't capacity basically bottleneck? Yes, whichever hits bottleneck, that is the capacity.

## Response time
- Response time is "request completed time" - "request sent time from the client"
- So, entering of server queue and complete processing time by server.

## Speed Up && Slowdown
- We USUALLY measure `speed up` and `slowdown` by comparing response time.
- Speed up = T<sub>old</sub> / T<sub>new</sub>, where if Time_new had better response time, then Speed-Up > 1
- Slowdown = T<sub>new</sub> / T<sub>old</sub>

## Terms
- Arrival rate: The average number of arrival per time unit to the system.
- Inter-arrival time: The time interval between consecutive arrivals. Often exponentially distributed in simple model.
- Service rate: The average number of services completed per time unit by the server
- Utilization: percentage of time when the server is busy

## Server models
- M/M/1: Poisson arrival(M), exponential service time(M), single server(1)
- M/M/1/k: Extension of M/M/1 where system's queue has finite capacity size of `k`. If the system is full, then new     arrivals are rejected. 
- M/M/N: Multiple M/M/1 servers with 1 shared queue (infinite)
- N*M/M/1: Multiple M/M/1 servers with EACH SERVER having its own queue (infinite)
- M/D/1: A single server with poisson arrivals and deterministic (constant rate of) service times.
- M/G/1: A single server with poisson arrival and general distribution

## What the hell are those symbols mean?
Before we jump into theory portion of the scheduling, let's understand what each symbols mean.
- ρ (rho) is the utilization (in percentage).
- λ (lambda) is the arrival rate.
- μ (mu) is the service rate.
- w is the number of requests waiting in the QUEUE
- q is total number of request in the SYSTEM
- T<sub>s</sub> is the service time for a request (how long the requests were handled for)
- T<sub>w</sub> is the waiting time in the queue
- T<sub>q</sub> is the response time (T<sub>q</sub> = T<sub>w</sub> + T<sub>s</sub>)
- τ (tau) is the task or job.

### Little's Law
ρ = λ * T<sub>s</sub> = λ / μ

Notice that:
1. 1/μ = T<sub>s</sub>
2. λ < μ. Thus, ρ < 1. 

    Ok, but why? 
    Because if we have more arrival rate of the job than the service rate, then the response time will reach INFINITY! (Impossible to predict!)

|| T<sub>q</sub> is the response time (T<sub>q</sub> = T<sub>w</sub> + T<sub>s</sub>)
- q = λ * T<sub>q</sub>
- w = λ * T<sub>w</sub>

|| Also by substitution:
- q = w + p


## Network of Queues
- definition:
    - In an network of queues, each queue can be analyzed in isolation of all others. Then add them up together.
- Assumptions: 
    1. System is in a steady state (Input and output are equal. What goes in, goes out)
    2. Arrivals from outside are poisson distributed
    3. Service times for all servers are exponential
    4. No finite queues (For example: M/M/1/K model)

### Jackson's Theorem
1. Split 
    - A single incoming arrival rate λ splits into two branches based on probabilities `p` and `1-p`.
    - Example:
    ```
            λ
            |
            v
          +---+
          | A |
          +---+
           / \
          /   \
         /     \ 
        v       v
       pλ     (1-p)λ
      +---+    +---+
      | B |    | C |
      +---+    +---+
    ```
    **Explanation**:
    - `A` receives an arrival rate `λ`.
    - After processing, it splits into two branches:
    - `B` with arrival rate `pλ`.
    - `C` with arrival rate `(1-p)λ`.
2. Merge
    - Two incoming arrival rates `λ1` and `λ2` merge into a single stream.
    - Example:
    ```
       λ1        λ2
        \       /
         \     /
          v   v
          +---+
          | A |
          +---+
            |
            v
            λ = λ1 + λ2
    ```

#### Example problem:
Q1.  There are system `A`, `B`, and `C`. All the system is M/M/1 model.
    During System `B` 45% of output goes back into System `A` for re-evaluation.
    During System `C` 20% of output goes back into System `A` for re-evaluation.
    System `A` receives input of 12 requests. (λ = 12) Graph the flow.

```

        +---+      +---+     +---+
λ -->   | A |  --> | B | --> | C | --> Output
    ^   +---+      +---+  |  +---+  |
    |                     |         |
    |_____ 0.45*λ_B ______|         |
    |__________ 0.2 *λ_C ___________|

```
λ<sub>A</sub> = λ + 0.45 * λ<sub>A</sub> + (1 - 0.45) * 0.2 * λ<sub>A</sub><br/>
λ<sub>A</sub> = 27.27<br/><br/>
λ<sub>B</sub> = λ<sub>A</sub> (what goes in, goes out)<br/><br/>
λ<sub>C</sub> = (1 - 0.45) * λ<sub>A</sub> = 14.9
<hr/>

### Generalized Processor Sharing (GPS)
- A resource sharing model where the server capacity is divided among multiple jobs. (Example: 50% of resource for task `A`, other 50% for task `B`)
- The key we need to take away from GPS is that `there is no perfect split due to limitation of granularity`

### Resource Management
1. Resources are fairly utilized (Fairness! aka the presence of starvation)
2. Requests can be classified according to some metric of importance ( Priority so we can finish the task on time~! :> )

## State
- Stateless: The service time for a request is independent of the history of previous requests (Ex: CPU)
- Stateful: The service time for a request is depended on previous request due to factors like caching (Ex: CPU with cache memory, where cache hit/miss effect performance!)

## Interrupt
- Pre-emptive: Resource can be interrupted while servicing a request and resumed later (Ex: save the current state (like take a snapshot of current system's memory addresses of stack pointer, instruction pointer, flags, and registers))
- Non-preemptive: Resources can NOT be interrupted while servicing a request.

Response time: The time elapsed from when a job is ready to when it finishes. 

## Schedule Policies
- First In First Out (FIFO): Non-preemptive scheduling where the requests are processed in order.
- Round Robin (RR): Pre-emptive version of FIFO. This prevents long jobs from dominating. (Smaller the quantum = more fair it is. But watch out for the overhead since context switching can be costly operation!)
- Shortest Job Next (SJN) or First (SJF): Non-preemptive scheduling algorithm that prioritizes the shorter job to minimize the overall response time. (Bad starvation for the longest job length, making the system `less predictable` than FIFO)
- Shortest Remaining Time (SRT): Pre-emptive version of SJN that further optimize response time for short task by allowing jobs to be interrupted if shorter job arrives. (Even WORSEEEEE starvation than the SJN!)
- Highest Slowdown Next (HSN): Non-preemptive scheduling strategy designed to address starvation problem in SJN and SRT by prioritizing job based their accumulated `slowdown`. 

    ```How slowdown is measured:```

    Slowdown = (t + c<sub>i</sub> + α<sub>i</sub>) / c<sub>i</sub>

    where 
    - t: current time
    - c<sub>i</sub>: length of the job
    - α<sub>i</sub>: arrival time of the job
    
    meaning the longer the request has been in the system (cases like `large job length requests`), the more slowdown it will accumulate. Which leads to prioritizing the large job that was sitting in the server at certain point.

## Wait, but how do we find the job length in real time system?
- Well, it is impossible to know incoming `request` or the `job length`
    - so we can estimate the job length by averaging the previous requests
- Here is how we could average job length
1. Simple Averages
    - C(n) = (C(n-1)*(n-1) + C<sub>i,n</sub>) / n
    - The issue with all time mean would be weight might take a long time to adjust. (The most recent data won't be reflected for our estimated job length. Think of why this would be important! Ex: Netflix, AWS, Google, and Microsoft servers)
2. Sliding Window Average
    - is a method to estimate job lengths while giving higher importance to recent observations by discarding older ones.

    w = window size (number of considered jobs)

    C(n) = C(n-1) - ((C<sub>i,n</sub> - w) / w) + ((C<sub>i,n</sub>) / w)
3. Exponentially Weighted Moving Averages (EWMA)
    - This is a method to better estimate trend by emphasizing recent observation while gradually reducing weight of older data

    How?

    C(n) = α*C<sub>i,n</sub> + (1-n)*C(n-1)

        where α ∈ (0,1]

        and α is the weight of how much you want to emphasize the newest job length


## Disk Scheduling Algorithm
- If we look at the HDD or the old disk hard drive, we see that there are disk head, which reads the data, and disk itself(call it cylinder). Within the cylinder, there are tracks and sectors where the data is stored. In order to read the data at the track's sector, we need to move the disk head to the track and read when the disk is rotated to the sector that we want to read. Now the question is, how can we optimize the disk head movement where it also has fairness?
    - First Come First Serve (FCFS): The disk head moves to the requested data location that arrived in order. (This has poor efficiency and high head movement ඞ)
    - Shortest Seek First (SSF): The disk head moves to the most closest requested data location from current head position. (Sure the head movement is minimal and efficiency is great. But the starvation great or the fairness shit)
    - SCAN (elevator): Disk head moves like an elevator. It goes up and down one track at a time. All the way to the top of the track and to the bottom of the track. (This approach is better than FCFS and SSF. BUT! can be better) 
    - C-SCAN (one way elevator): This is just like SCAN except, once it reaches to the top, it drops to the bottom (doesn't have to go all the way up or drop down to bottom if there is no request that is located at the very top or bottom). This approach is very efficient, predictable, and fair. It's head movement is good. 

        Now there is `Completely Fair Queuing`(CFQ) where each application or task gets its own queue for disk requests. Then, there is `quantum` of request for each queue, q<sub>cfq</sub>, which determines the number of request to be handled per queue. (Ex: q<sub>cfq</sub> = 2, then it takes 2 requests from the application `A`, 2 requests from the application `B`, and so on). Then the requests are served in round-robin between application queue (which reorders combined queue, and served like C-SCAN policy)

## Real Time Systems!
- System where correctness depends on meeting time constraint (a.k.a deadline)./

### Predictability
- A system is considered predictable if a gap between its `best case execution time`(BCET) and `worst case execution time`(WCET) is small!
- In `Real-time` system, we need to make sure `WCET` is `under deadline`. (Why? Things like airbag in car needs to meet the deadline or else the person is gonna be `deadlined`)

### Hard real time system scheduling
1. Rate Monotonic Scheduling (RM): Tasks with shorter periods (T<sub>i</sub>) are assigned higher priorities.
    - Schedulability Analysis:

        To determine if assigned priorities will meet deadline, we need to use `Utilization Bound Test` aka `Liu and Layland`.

        U = Σ(C<sub>i</sub> / T<sub>i</sub>)

        If U ≤ m*(2<sup>1/m</sup> - 1), then the tasks are schedulable under RM scheduling. 
        - However, this does NOT mean that if utilization is greater than max utilization, then it is not schedulable. If this is the case, then we need to graph it out or use `RM Completion Time Test` (we will not address the equation, and example in here. But the `Completion Time Test` looks at the worst case execution time of each task. As we evaluate each time, we should see the completion time stabilizing to one number. If the stabilized number is under the WCET, then it is schedulable)
2. Earliest Deadline First (EDF): EDF is significant improvement over static scheduling algorithms like RM. At any scheduling event, EDF assigns priorities based on `absolute` deadline. Meaning which ever task needs to be prioritized to meet the deadline first will go. 
    - Schedulability Analysis:
    
        To determine if assigned priorities will meet deadline, we need to use `Utilization Bound Test`.

        U = Σ(C<sub>i</sub> / T<sub>i</sub>)

        If U ≤ 1, then the tasks are schedulable under EDF scheduling. 

3. RM-First Fit (RM-FF): 
    - Tasks are sorted and assigned to `processors` in first-fit matter. (We need to have MULTIPLE processors or resources)
    - For each task, check schedulability on the first processor. If it fails, then move to next processor.
    - RM-FF guarantees schedulability if:

    U = Σ(C<sub>i</sub> / T<sub>i</sub>)

    If U ≤ N*(√2 - 1), then the tasks are schedulable under RM-FF scheduling. 

    where 
    - N: number of processors
    - C<sub>i</sub>: WCET of τ<sub>i</sub>
    - T<sub>i</sub>: Period of τ<sub>i</sub>

    In another words, for `each` processors, the `max utilization bound` is `(√2 - 1)`

3. EDF-First Fit (EDF-FF): 
    - Tasks are sorted and assigned to `processors` in first-fit matter. (We need to have MULTIPLE processors or resources)
    - For each task, check schedulability on the first processor. If it fails, then move to next processor.
    - EDF-FF guarantees schedulability if:

    U = Σ(C<sub>i</sub> / T<sub>i</sub>)

    If U ≤ (β * N + 1) / (β + 1), then the tasks are schedulable under EDF-FF scheduling. 

    where 
    - N: number of processors
    - C<sub>i</sub>: WCET of τ<sub>i</sub>
    - T<sub>i</sub>: Period of τ<sub>i</sub>
    - β = math.floor(1/max(C<sub>i</sub> / T<sub>i</sub>))

    In another words, for `each` processors, the `max utilization bound` is `1`


# Synchronization | Mutual Exclusion
- **Definition**: Management of shared resource in concurrent computing
- Why do we need to worry about the shared resource?
    - Simply to avoid the `data race` or `race condition`. What that really means is the `critical section` where the storage of the shared data must be updated correctly. In order to protect the critical section, we need to have one write operation at a time. Let's me give you example.

        Let's say we have process `A` and `B`. They are doing the same operation, which is incrementing 1 integer value at a time:

        ```c
            """
                let's say there is 
                global variable (shared data)
                int i with value 10;
            """
            // Start of critical section
            i += 1;
            // End of critical section
            printf("Process %c wrote i value to: %d\n", 'A or B', i);            
        ``` 
        If the process A and B are running concurrently (at the same time), then we could have something like:

        ```bash
            Process A wrote i value to: 11
            Process B wrote i value to: 11
        ```

        How can this happen?
        <table style="width:100%">
            <tr>
                <th>Iteration</th>
                <th>Process A</th>
                <th>Process B</th>
            </tr>
            <tr>
                <td>1</td>
                <td>mov $i, %eax</td>
                <td></td>
            </tr>
            <tr>
                <td>2</td>
                <td></td>
                <td>mov $i, %eax</td>
            </tr>
            <tr>
                <td>3</td>
                <td></td>
                <td>inc %eax</td>
            </tr>
            <tr>
                <td>4</td>
                <td>inc %eax</td>
                <td></td>
            </tr>
            <tr>
                <td>5</td>
                <td>mov %eax, $i</td>
                <td></td>
            </tr>
            <tr>
                <td>6</td>
                <td></td>
                <td>mov %eax, $i</td>
            </tr>
        </table>

        Notice that i is getting assigned value `11` twice instead of `11` then `12`?

        Now the question is, how can we resolve the data race?

## Dekker's algorithm | Peterson's algorithm (when we have 2 processes)
- This is example version of peterson's algorithm:
```js
/* Global, shared variables */
var flag[i] = false;
var flag[j] = false;
var turn = i;

Process Pi:
    repeat:
    /* remainder section */
        flag[i] = true;
        turn = j;
        while (flag[j] == true && turn == j);
        /* critical section */
        flag[i] = false;
    /* remainder section */
    forever
```

- The main idea for these algorithm is, it is utilizing flag and turns to give each process a chance to enter critical section before themselves does. (Fairness(no deadlock) and one process at a time in critical section)
- This would not work for case where we have more than 2 processes running concurrently. (because of turn)

## Bakery algorithm (more than 2+ processes)
This is just like real life bakery shop. If customers (processes in our case) want to get bread, then they have to be in a line / grab a ticket. When it is their turn (based off the ticket number), then they get to grab the bread (equivalent to entering critical section).

1. **Ticket Assignment**: Each process selects a ticket number greater than the current maximum ticket among all processes.
2. **Wait for Turn**: Processes compare ticket numbers. The process with the smallest number gets the critical section. If two processes have the same ticket, a tie is broken using process IDs.
3. **Critical Section**: The process with the smallest ticket number enters the critical section.

```c
while(true) {
    choosing[i]=true;
    number[i]=max(number[0],…,number[n-1])+1;
    choosing[i]=false;
    for (j=0;j<n;j++) {
        while (choosing[j]);
        while ((number[j]!=0) && ((number[j],j) < (number[i],i)));
    }
    // critical section;
    number[i] = 0;
    // non-critical section;
}
```

## Ok, besides all that, is there a simpler way of handing synchronization?
- Yes you can use `Test-And-Set` or `Spinlock` or `Semaphore`

We will touch the `spinlock` and `semaphore` since they come across very frequently and spinlock calls test and set.

### Spinlock
- This is busy-waiting(polling) method to keep check if the critical section is free or busy (if free, then enter. Otherwise, just keep checking). 
- This method wastes clock-cycle. However, it might be better to use spinlock in a case where the operation is very quick. Why? because semaphore has more overhead than the spin-lock. If the overhead is almost the same as the system busy-time, then it might be better to use spinlock which has much smaller overhead than semaphore.

### Semaphore
- A semaphore is a synchronization mechanism that:
  - **Keeps a count** of available resources.
  - **Maintains a wait queue** for processes that need to access those resources, but not yet available.

- **How it Works**:
  1. A process attempting to enter the critical section decrements the semaphore value (often referred to as `wait()` operation, (`sem_wait()` in c lang)).
     - If the semaphore value(resource count) is greater than or equal to 0, the process will be allocated a resource and proceeds.
     - If the value is less than 0, the process is placed in the wait queue since all the resources are being used.
  2. When the critical section is freed, the semaphore count is incremented (often referred to as `signal()` operation (`sem_post()` in c lang)).
     - If there are processes in the wait queue, the next process is dequeued and allowed to proceed.

- **Advantages**:
  - Allows multiple processes to access the critical section when resources are available (useful for managing multiple instances of a resource).
  - Avoids busy-waiting, as it relies on a signal mechanism to notify waiting processes.
  - Note that if the semaphore count is set to `1`, then it is same thing as `mutual exclusion`

- **Example**:
```c
// Pseudo-code for semaphore
semaphore s = 1; // Initialize semaphore (1 resource available)

// Process attempting to enter critical section
P(s); // Wait operation (decrement semaphore)
// Critical section
V(s); // Signal operation (increment semaphore)

// Semaphore implementation with wait-queue
P(s) {
    s--;
    if (s < 0) {
        enqueue(current_process, wait_queue);
        block(); // Process waits until signaled
    }
}

V(s) {
    s++;
    if (s <= 0) {
        process p = dequeue(wait_queue);
        wakeup(p); // Signal waiting process
    }
}
```


## Read-Copy-Update (RCU)
- **Definition**: A synchronization mechanism optimized for scenarios where shared data is read frequently but updated infrequently.
- **Key Features**:
  - Allows **simultaneous reading and updating** without blocking readers.
  - Readers access shared data without locks, avoiding contention.
  - Writers modify a **new copy** of the data and update references atomically. (changes pointer to the kmalloc() of the new data. This is not fork() or dup() sys call since we are not making new process.)
  - Widely used in the **Linux kernel** for efficient multi-processor data sharing.

---

### How It Works:
1. **Readers**:
   - Can freely read the shared data without acquiring locks.
   - Ensure no blocking or overhead, making reads very fast.

2. **Writers**:
   - Create a **new copy** of the shared data when an update is required.
   - Modify the new copy independently of readers.
   - Atomically update a reference to point to the modified data, ensuring consistency.

3. **Grace Period**:
   - The old version of the data is not immediately deleted.
   - It remains accessible until all readers referencing it finish their operations.
   - After this grace period, the old version can be safely freed.

---

### How Readers and Writers Coexist:
- **Before the Update**:
  - Readers access the old version of the data.
- **After the Update**:
  - New readers see the updated version, while existing readers continue using the old version.
- **Synchronization**:
  - Writers ensure updates are atomic and do not interfere with ongoing reads.

---

### Example Use Case:
RCU is ideal for **read-heavy workloads** where contention among multiple readers and occasional writers needs to be minimized, such as:
- Maintaining routing tables in a network stack.
- Kernel data structures in operating systems.

### Advantages:
- **High Performance**: Non-blocking reads ensure fast access in multi-threaded environments.
- **Scalability**: Performs exceptionally well in systems with a high read-to-write ratio.

### Limitations:
- **Complexity**: Implementing RCU can be more complex compared to simpler synchronization primitives like locks.
- **Memory Overhead**: Requires keeping multiple versions of the data until the grace period ends.

## Deadlocks
A **deadlock** occurs when processes are unable to proceed because they are each waiting for resources held by other processes, creating a cycle of dependency. 

### Characteristics of Deadlock:
1. **Multiple Processes and Resources**: At least two processes and resources are involved.
2. **Limited-Capacity Resources**: Resources like printers or mutexes can only be used by one process at a time.
3. **Hold and Wait**: Processes hold some resources while waiting for additional ones.
4. **Circular Wait**: A closed chain of dependency where each process waits for a resource held by the next in the cycle.

---

### How to Avoid Deadlocks?

#### Banker's Algorithm:
The **Banker's Algorithm** avoids deadlocks by ensuring the system remains in a **safe state**, where all processes can complete without entering deadlock. It works by simulating resource allocation and determining if it leaves the system in a safe state.

---

### Steps of the Banker's Algorithm:

1. **Initial Information**:
   - Obtain the **Maximum** and **Allocated** and **Available** resources for each process.
   - Calculate the **Needed** resources for each process using:
     ```
     Needed = Maximum - Allocated
     ```

2. **Check Safety**:
   - Evaluate each process to see if its **Needed** resources can be satisfied with the **Available** resources.
   - If a process's `Needed <= Available`, allocate its resources, let it finish, and release its **Allocated** resources back to the pool(aka `available`). Update `Available` accordingly.

3. **Simulate Execution**:
   - Repeat the above step for all processes. If all processes can finish in a specific order, the system is in a **safe state**.
   - If a process cannot proceed (i.e., `Needed > Available`), skip it and check the next one.

4. **Grant Requests**:
   - Only grant resource requests if the system remains in a safe state after allocation.

5. **Record the Safe Sequence**:
   - The order in which processes execute during the simulation is recorded as the **safe sequence**. This guarantees that no deadlock occurs.

---

### Example:

#### Initial Conditions:
| Process | Maximum | Allocated | Needed  | Available |
|---------|---------|-----------|---------|-----------|
| P1      | 7       | 2         | 5       | 3         |
| P2      | 5       | 3         | 2       |           |
| P3      | 3       | 2         | 1       |           |

1. Calculate `Needed` for each process:  
    - P1: 7 - 2 = 5 
    - P2: 5 - 3 = 2 
    - P3: 3 - 2 = 1

2. Check if `Needed <= Available`:
- (x) **P1**: `Needed = 5`, `Available = 3` → Can't proceed. So, we come back to it later

- (o) **P2**: `Needed = 2`, `Available = 3` → Can proceed.  
  After P2 finishes, `Available = 3 + 3 (P2 Allocated) = 6`.

- (o) **P3**: `Needed = 1`, `Available = 6` → Can proceed.  
  After P3 finishes, `Available = 6 + 2 (P3 Allocated) = 8`.

- (o) **P1**: `Needed = 5`, `Available = 8` → Can proceed.  
  After P1 finishes, `Available = 8 + 2 (P1 Allocated) = 10`.

3. Safe Sequence: **P2 → P3 → P1**

Since all processes can complete, the system is in a **safe state**.

---

### Advantages of Banker's Algorithm:
- Prevents deadlocks by ensuring resource allocation only occurs if the system stays in a safe state.
- Works well in systems where maximum resource demands are known in advance.

### Limitations:
- Requires prior knowledge of **maximum resource needs** for all processes.
- High overhead for systems with frequent resource allocation requests.
- Doesn't handle dynamic resource demands efficiently.
