# Monday Lecture : The Link Layer

## Link layer vs network layer
The role of the **link layer** is to take one packet from one end of *one link* to the other end.
The role of the **network layer** is to take a packet from one end of *the network* to the other end.
### IP subnet point of view 

<u>A single IP subnet (the blue ellipsis)</U>
![[Pasted image 20240519143726.png]]


The role of the link layer is to take a packet across a single physical link.
The role of the network layer is to take a packet across the entire network, i.e., the entire IP subnet

### Internet point of view
![[Pasted image 20240519144703.png]]
The Internet is a network of IP subnets, i.e., a network of networks (hence the name “*Inter-network*” or Internet).

From the point of view of the Internet, a “link” is a path segment across a single IP subnet. 

- The role of the link layer is to take a packet across a single “link,” i.e., a single IP subnet.
- The role of the network layer is to take a packet across the entire network, i.e., the Internet


## Link layer
So the link layer can mean two thing : 
1. <u>Link layer of an IP subnet</u>: takes packet from one end of one **physical link** to the other end
2. <u>Link layer of the Internet</u>: takes packet from one end of one **IP subnet** to the other end

### Link layer of an IP subnet

#### Link layer services

- Error detection 
	Meaning that the two devices an the ends of a physical link participate in a protocol to detect and drop corrupted packets.
	They can do this by relying on checksums (like UDP and TCP).
- Reliable data delivery
	Meaning that the two devices at the ends of a physical link participate in a protocol to detect and recover from corruption and loss.
	They can do this by relying on timeouts, ACKs, retransmissions… (like TCP).
- Medium access control (MAC)
	This is useful when multiple links *share the same physical medium* (e.g., multiple wireless links share the same wireless environment).
	
	In this scenario, when a sender transmits, it needs to **detect** whether someone else transmitted at the same time, in which case it cancels the transmission and “backs off,” meaning it **retries later.**


<u>Why do we do reliable data delivery at the link layer ??? the TCP does it anyway</u> 
Because it is a useful performance optimization: (**big example**)
*** 
Consider the topology where two end-systems communicate over a long sequence of physical links.
Suppose the first link is a wireless link that suffers from occasional “glitches,” i.e., short time intervals of heavy packet loss.
Suppose the source end-system sends a sequence of segments to the destination end-system during such a glitch, and they all get lost on the wireless
link.
Think about how TCP recovers from such loss:
- The source end-system times out waiting for ACKs.
- It resets its congestion window to 1 MSS and retransmits the oldest unacknowledged segment.
- When it receives an ACK for this segment, it increases the congestion window to 2 MSS’s, retransmits the next two segments, and so on.
Two important points:
- The source end-system must wait for a TCP timeout before acting.
- The throughput from the source to the destination end-system drops significantly.
Now suppose the wireless link provides reliable data delivery (at the link layer). This means that the two devices at the ends of the wireless link (the source
end-system and the first switch) try to **locally** recover from any packet loss that occurs on the wireless link.
Think about what happens:
- The link-layer part of the source end-system times out waiting for ACKs from the (link-layer part of the) first switch and retransmits the lost packets
(using some congestion-control algorithm that we have not discussed).
- The transport-layer part of the source end-system (the TCP code running at the source end-system) does not perceive any packet loss, so it does
not timeout and does not reset its congestion window.
So: The link layer around the specific problematic link recovers quickly from the loss, hence TCP does not perceive the loss. This means no TCP timeout
and no TCP congestion-window reset, hence higher throughput from the source to the destination end-system.
*** 

### Link layer of the Internet
There exist many different types of IP subnets, but we will discuss only the most popular one: ***Ethernet***.

#### Addressing
What type of addressing does Ethernet rely on?

![[Pasted image 20240519151139.png]]
Every end-system in this IP subnet has at least one network interface, and every network interface has **a link-layer address** (also called **MAC address**, or Ethernet address)

The thick vertical lines in this picture represent network interfaces. 5c:f9:38:a4:00:76 is the MAC address of the network interface on the left, while ab:ff:4d:a4:bb:af is the MAC address of the network interface on the right

So, when one end-system sends a packet to another end-system, in the same IP subnet, the packet carries a link-layer header (also called MAC header, or
Ethernet header), and inside this header there is a source MAC address and a destination MAC address (plus a few other fields).

<u><b>MAC address</b></u>
48 bit number 
	typical format : 1A-2B-DD-78-CF-CC
	The value of each byte as hexadecimal
**Flat** 
	Not hierarchical like IP address
	Not location dependent
#### Forwarding 
How do Ethernet switches forward packets?

![[Pasted image 20240519164253.png]]

Each switch names its network interfaces (also called links, or ports), and keeps a forwarding table that maps MAC addresses to links.

When a packet arrives, the switch reads the destination MAC address from the MAC header, looks it up in the forwarding table, and identifies the correct output link for this packet

This is called <u><b>L2 forwarding</b></u> (not the same as IP forwarding)
![[Pasted image 20240519164452.png]]
#### Learning
Who populates the forwarding tables of Ethernet switches?

![[Pasted image 20240519164646.png]]
Keep in mind that the packet can go in both direction, 

The forwarding table of a link-layer switch is initially empty.

The switch fills the table based on the traffic it receives.
For example, if it receives a packet with source MAC address 10 at link (a), it adds an entry to the forwarding table, indicating that:
if, in the future, it receives a packet with destination MAC address 10, it should forward that packet to link (a).

If the switch receives a packet with a destination MAC address for which no entry currently exists in its forwarding table, it broadcasts that packet to all links.

But if we do **naive broadcasting** we get forwarding loops !!
 ![[Pasted image 20240519164954.png]]
 We said that, when a switch receives a packet with unknown destination MAC address, the switch broadcasts the packet.
If all switches broadcast packets with unknown destination MAC addresses to all links (except the one where the packet arrived), there may be **forwarding loops**

To avoid that we use : 

<u>Spanning tree</u>
To avoid this, all the switches in an IP subnet participate in a protocol that creates a “spanning tree.”
This is a **subgraph** of the IP subnet, which includes all the end-systems, routers, and switches of the subnet, but only a subset of the links. It includes just
enough links to reach all the end-systems and network devices.
There may exist many spanning trees in an IP subnet.

Example: 
![[Pasted image 20240519165308.png]]
#### Address resolution 
In an Ethernet, how does an end-system learn the destination MAC address that it should write on a packet that it wants to send?

<u>Same IP subnet</u>
![[Pasted image 20240519171746.png]]
Suppose Alice wants to send a packet to Bob, who happens to be in the same IP subnet as Alice.

The first thing she needs is Bob’s IP address, which will be the packet’s destination IP address.
We know how she gets this: she asks a local DNS server.

The second thing she needs is Bob’s MAC address, which will be the packet’s destination MAC address.To get this, she sends out a special request that states Bob’s IP address. This request (called an “ARP request,” as we will see later) has a special destination MAC address, which is called a “broadcast address,”
meaning that the request is addressed to all the end-systems and routers in the local IP subnet.
So, every switch that receives this request broadcasts it.
Eventually, the request reaches Bob.
![[Pasted image 20240519171758.png]]
When Bob receives the request, he recognises his IP address and sends a special response (called an “ARP response,” as we will see later), which states his MAC address.
When Alice receives Bob’s response, she learns his MAC address and is finally ready to send him a packet (because she now knows both what destination IP address and what destination MAC address to write on the packet).

<u>Different IP subnet</u>
![[Pasted image 20240519172033.png]]
Now suppose Alice wants to send a packet to Bob, who happens to be in a different IP subnet from Alice.
As in the previous scenario, the first thing she needs is Bob’s IP address, which will be the packet’s destination IP address.
We know how she gets this: she asks a local DNS server.

Again, as in the previous scenario, the second thing Alice needs is the correct destination MAC address to write on her packet. That’s the MAC address of router R’s network interface that belongs to the local IP subnet.

By configuration, Alice knows that R is her “default gateway,” meaning that any traffic that Alice sends outside the local IP subnet will exit the subnet
through R. More precisely, Alice knows R’s IP address.
Hence, Alice broadcasts an ARP request that states R’s IP address.
Eventually, the ARP request reaches R.

When R receives the ARP request, it recognizes its IP address and sends an ARP response that states its MAC address.
When Alice receives R’s ARP response, she learns R’s MAC address,
and is finally ready to send her packet to Bob


#### Address Resolution Protocol (ARP)
The goal of ARP is to map IP address to MAC address
	Alice knows destination IP address 
	-> which destination MAC address to use??
**How** : broadcast + caching 
	Alice brodcasts her request
	End-systems and routers caches ARP responses

<u>Broadcasting</u>
Alice sends request to special, broadcast destination MAC address
- FF-FF-FF-FF-FF-FF
Reaches every end-system and router in the local IP subnet


### Switch and router addresses
Yet both switches and routers have both MAC and IP addresses
- 1 MAC address + >= 1 IP address per network interface
For various practical reasons 
- To be reachable by an administrator
- For link testing
- A router needs an IP address to respond to ARP requests
- A router that acts as a NAT gateway needs an IP address for NAT


## SUMMARY : 3 network levels
• IP subnet
- L2 forwarding, L2 learning
• Autonomous System (AS)
- IP (L3) forwarding, intra-domain routing
• Internet
- IP (L3) forwarding, inter-domain routing (BGP)

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
