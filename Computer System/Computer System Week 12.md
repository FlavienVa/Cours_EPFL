

# Wednesday Lecture : Concurrency 


## Importance of concurrency and parallelism 
### Why do we care about concurrency
1. Improve **responsiveness**
	• Efficiently utilize the computation power of resources
		• CPU speed is quite faster than disk → minimize the processor idle time
	• Multiple compute units are present on one computer
		• Utilize the compute units to improve the performance/response of applications
2. **Modularize** programs
3. Maintainability or **understandability** of the program


### Concurrency vs parallelism 
<u>Concurrency</u>
Multiples tasks execute in an **overlapping** time period (but not
necessarily simultaneously). In computing, the CPU achieve concurrency by slicing time among threads, and the CPU switches between tacks very quickly. 

<u>Parallelism</u>
Focuses on performing multiple tasks or several parts of a single task **simultaneously** by using **multiple** processing units. In computing, we have multiples CPU that can execute a different task at the same time. 

## Execution context : Thread as an abstraction
### The thread abstraction 
A process can have multiple threads with : 
- A shared address space (heap, data, text)
- Shared file descriptors

Each thread: 
- Has its <u>own stack</u>
- Is independently scheduled by the OS
- Can issue system calls 
![[Pasted image 20240515084606.png]]

### Why use threads 

**shared address space to enable easy sharing of data** 

<u>Parallelism</u>
Exploiting multiple CPUs

<u>Concurrency</u>
Mask long latency of IO
	Minimize waiting time by scheduling other threads / tasks
High priority GUI threads / low priority worker threads

### The API 
`pthread_create(&t,&attr,(void*) (* start_routine)(void * ),args) ` 
->Create a new thread 
->The pthread library calls * start_routine(args)

`pthread_join(&t,void **retval)` 
->Wait for the completion of a thread 
	(return from * start_routine)

## Abstraction : Locks to protect shared data
### Issues with threads 
• Uncontrolled scheduling of threads → **thread interleaving**
• Thread interleaving can introduce:
	• A **race condition** occurs when the timing or order of events affects the correctness of the program
	• A **data race** when one thread is accessing a mutable variable while another thread is writing to it without any synchronization
• Due to race condition, we observe different results for different executions
	• Thread interleavings leads to non-deterministic behavior

### Atomic (boum)
Problem of the incrementing of a loop by two threads (problem presented in course)

In other words, the execution of all these three instructions should be atomic:
*An operation that appears to be instantaneous from the perspective of all threads*

#### -> We can <u>ensure atomicity through mutual exclusion</u> 
Data race occurs on shared memory variables
Code block that concurrently accesses shared region is called critical section

To ensure correct program, we must **mediate** the access to the critical section 
	Achieve atomicity: Execute a critical section as an uninterruptible block

-> **Mutual exclusion** : Only one thread can execute this critical section at any point
in time, while others wait; Synchronizes the access to the critical section. 

***Locks* are one such mechanisms that ensure atomicity via mutual exclusion**


### Locks : basic idea
- Lock variables protect critical sections via **mut**ual **ex**clusion (**mutex**)
- All threads competing for a critical section share a lock
- Only one thread succeeds at acquiring the lock (at a time)
- Other threads must *wait* until the lock is released
### Lock implementation 

Not important

## Mutexes : locks for threads

### Mutex : sleepable locks
Waiters park themselves (go to sleep) if they do not acquire the lock
**Idea**
- Try to acquire the lock as fast as possible; if failed, then go to sleep using a waiting queue
- Wake up thread(s) when a lock is released 
	- Can wake up all threads from the queue to race for the lock 
	- Selectively wake up one thread, from the queue, to ensure fairness

<U>Implementation</U>
`pthread_mutex()` 
![[Pasted image 20240515094744.png]]
