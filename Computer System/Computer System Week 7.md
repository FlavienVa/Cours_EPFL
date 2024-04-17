
# Monday Lecture : Internet Architecture 
The Internet is the part, essential part, that connect the devices to each-other. We need to understand how this Internet **communicate** information between devices.
## What's on an Internet path

### Architecture of Internet line/connexions
<u>Phone line</u>
This way is the easiest, because the infrastructure was already there. The speed can vary between 10Mbps-10Gbps, depending on the technology of the cable. 
![[Pasted image 20240408093354.png]]
- The **end-system** is your home.

<u>Cable TV line</u> 
Same idea as the phone line, with 100+ Mbps. But the architecture is a **tree** and not a direct connexion. 
![[Pasted image 20240408093811.png]]
The *phone line* as a more predictable speed. The cable TV line was made to watch the **same TV program**, so it was a cleaver way to distribute all the data to all the house, but today every one use Internet for various usages. This make this architecture not efficient when a lot of people are using it and require protocol to **not share** data. 

<u>Ethernet Cable</u> 
The speed can vary between 1-100 Gbps.
![[Pasted image 20240408094403.png]]


### Internet Service Providers (ISPs)
We need providers to give us access to the internet and pass internet request.

![[Pasted image 20240408095034.png]]
So the BETTER would be to have **one** big ISP which take all internet request, BUT life etcc.. so we have multiple big ISPs the Tier-1 ISP who communicate between them. 
![[Pasted image 20240408095357.png]]

All the the Tier-1 ISPs can not have a direct connections, so we use a node to connect them called **IXP, Internet Exchange Provider** 
![[Pasted image 20240408095719.png]]

We also have an other participant to our super fiesta, the **content/cloud provider**, it communicates directly to the IXP, it also have a lot of caches to make the system faster. 
![[Pasted image 20240408102306.png]]

RESUME
Interconnected through a sophisticated hierarchy where content/cloud providers play an increasingly important role.
## How do apps communicate over the Internet?

### (Network) system calls
The Application Programming Interface (API) exposed to processes for Internet communication.

Simple examples of interaction : send, recv, connect, listen, accept... 

![[Pasted image 20240408102733.png]]
NIC : Network Interface Card 

### Role of each layer
![[Pasted image 20240408103619.png]]

Each layer provide an abstraction of what lies below to the layer above, it adds his own "header" to understand the data. 
![[Pasted image 20240408103932.png]]
Here we see that in Alice computer all the layer has added its header and it will now transform it in a signal.

Then a packet switch will analyse and change (if necessary) the link and network header. 
![[Pasted image 20240408104127.png]]
A packet switch **should not change or look** pass the link and network header. 

Then Bob will receive the signal and decode it header by header to get the data. 
![[Pasted image 20240408104301.png]]

RESUME
On the **same device**: each layer uses interface to communicate with layer below and above. 

**Across devices**: each layer uses header to communicate with the same layer of another device

They reduce complexity, improve flexibility but do not improve performance :(

### Name (identifiers)
![[Pasted image 20240408105108.png]]
We can see this as a address in real life. 

# Wednesday Lecture : Internet Performance

## How do we reason about network performance?

<u>Basic network performance metrics</u> 
- Packet loss 
	- The fraction of packets from src to dst that are lost on the way 
	- in %, for example 1% packet loss

- Packet delay
	- The time it takes for a packet to get from src to dst 
	- In time units, for example 10msec

- Average throughput
	- The average rate at which dst receives data
	- In bits per second (bps)
	- for example, dst receive 1 GB of data in 1 min; average = 133.34 Mbps

<u>Delay VS throughput</u>
Packet delay matters for **small** messages
Average throughput matters for **bulk** transfers

They are related to each other, but not in a obvious way. 
![[Pasted image 20240410093716.png]]

<u>Queuing delay</u> 
Given info on traffic pattern (arrival rate, nature of arriving traffic)

Characterized with statistical measures (average queuing delay, variance of queuing delay, probability that it exceeds a certain value)

It the arrival rate in the queue is **higher** that the departure rate the queuing delay approches **infinity** 

<u>Packet delay</u>
As saw earlier there is a log of component of packet delay : transmission, propagation, queuing, processing

So it depends on network topology, link properties, switch operation, queue capacity, other traffic

<u>Bottleneck link</u> 
![[Pasted image 20240410094629.png]]

The link where the traffic flows at the **slowest rate** is the bottleneck link

It could be because of 
- the link's transmission rate
- or because of queuing delay

## What kind of performance does the Internet provide? 

As a good metaphor we can see the switch content as a restaurant without reservation, where you treat client when they come (like a fast food for example)
The Resources management technique will be a restaurant WITH reservation, where you reserve one queue for a client.

<u>Switch content</u> 
- Queues 
	Store packets
- Forwarding table 
	Store meta-data
	Indicate where to send each packet
	![[Pasted image 20240410102302.png]]
It is a very efficient use of ressources, but it has unpredictable performance. 
+simpler to implement

<u>Resource management</u> 
- Packet switching 
	Packet treated on demand 
	Admission & forwarding decision: per packet
- (Virtual) circuit switching 
	Resources reserved in advance 
	Admission & forwarding decision: per virtual circuit 
	![[Pasted image 20240410101915.png]]
We got *predictable performance* but it can lead to  *inefficient use of resources* (if only the green one make the the request)


<u>Kinds of "circuits"</u> 
- Physical circuits
	separate sequence of physical links per communicating end-system pair
- Virtual circuits
	manage resources as if there was a separate sequence of physical links per comm. pair

Division of resources : 
- Time division multiplexing 
	divide time in time slots
	separate time slot per communicating pair
- Frequency division multiplexing 
	divide frequency spectrum in frequency bands
	separate frequency band per communicating pair
