# Monday Lecture 

## Mount points (recup of last week)

We have a lot of **files** systems: USB stick, Memory, NAS etc..

They are all mapped to a **commun root!**

**mount** == **Greffer** sur un arbre 

This is very beneficial because we end up with only <u>one name space</u>

For this operation we use: 
`mount <device> <dir>` 
Ex: `mount /dev/cdrom /mediam/cdrom` 
Super cool command : 
`df` --> report file system disk space

***!!!*** this is only an abstraction ***!!!*** 
If you want to <u>really</u> move the element you have to copy+delete across file system. (the command `mv` handle this transparently to users)

# IO devices

## How does a system perform I/O?

The IO is used to : Load programs and data from storage; Read/write packets from network; Read data from input devices....

We have once again a lot of **abstraction** aka : 
- Block cache : 
	- Caches blocks recently read from disks
	- Buffers recently read blocks
- Block device interface:
	- Single interface to many devices
	- Allows data read/written in fixed size blocks
- Device driver: 
	- Translates OS abstractions and hw specific IO device details
- MM IO, DMA, interrupts
	- Controls registers, bulk data transfers, OS notifications
![[Pasted image 20240325102319.png]]

***!!!*** **IO is slow :/  ***!!***
![[Pasted image 20240325102634.png]]

There are specific peripheral used to control IO, the <u>IO controller</u>
-IO controller supports IO devices and is responsible for handling their requests
-OS handles device management and exposes uniform interface to applications
-OS processes access these devices by reading/writing IO registers 
	Write commands and arguments, read status and results
![[Pasted image 20240325103244.png]]

Schema of the IO system : (to give an idea no need to know)
![[Pasted image 20240325103452.png]]

#### <u>Bus</u> 
Def: Buses are common set of wires for communication among hardware devices plus protocols for carrying out data transfer transactions

PCI are collection of fast channels or "lanes"
	PCI1: 4 GB/s bandwidth for 16 lanes
	PCI5: 64 GB/s bandwidth for 16 lanes


#### Canonical device 
![[Pasted image 20240325104355.png]]
CPU interacts with device controller
	CPU writes device registers 
Device internals are hidden to users ("abstraction" again)

OS configures and communicates based on the agreed protocol 
	 User device driver for the communication 
Device controller signals to the CPU either through *memory polling* or *interrupt*

## Operations parameters for IO
What are various operation parameters for IO between CPU and the device?
1. How does the CPU talk to a device?
2. Read/Write data at what granularity 
3. Read/write data in what pattern?
4. How does a device notify the OS?
5. What is the mechanism to transfer data to a device, i.e., transfer mechanisms?

#### 1. CPU talking to the device 
We got two important things : 
- **Port-mapped IO** : CPU uses designed IO ports
	- Each devices has an assigned port
	- Special instructions (in/out on x86) communicate with the device
- **Memory-mapped IO** : CPU uses load/store instructions 
	- Hardware maps control registers into the physical address space
	- IO accomplished via load/store instructions
**Both** are used in practice and architecture supports both approaches
Memory-mapped IO is predominantly in use for high performance devices
Fast storage devices; network devices; monitor

#### 2. The granularity 
Byte VS block 
	 Some devices provide single byte at a time (keyboard)
	 Other provide whole blocks (disks, NICs)
#### 3. Access pattern 
Sequential vs random 
	Some devices are accessed sequentially (tape)
	Other can be accessed randomly (disks, CD)
#### 4. How does a device notify the OS
Polling VS interrupts 
	IO interrupts : 
		- Device generates an interrupt whenever it needs service
		- **Pro** : handles unpredictable events well
		- **Con** : Interrupts have relatively high overhead 
	Polling (spinning)
		-  OS periodically checks a device specific status register (where IO devices have put the completion information)
		- **Pro** : low overhead 
		- **Con** : waste CPU cycle if infrequent or unpredictable IO operations 

Actual systems use a mix of polling and interrupts.

Another system is the **coalescing** (batching), the device wait until a certain time and then batch all the response and send everything. 

#### 5. What is the mechanism to transfer data to a device, i.e., transfer mechanisms?
We once again have 2 mechanisms : 
**PIO (programmed IO)** : CPU tells the device <u>what</u> data is : 
- One instruction for each byte/word 
- Efficient for few bytes; consumes CPU cycles proportional to data size

**DMA (direct memory access)**: CPU tells the device <U>where</u> data is :
- Give controller access to memory bus
- Ask to transfer data to/from memory directly
- Efficient for large data transfers


## Device drivers
The OS has to manage a lot of different devices, they all have their special protocol.. 

<u>Device drivers</u>

Drivers are an example of encapsulation
- Different drivers adhere to the same API
- OS only implements the support for APIs based on device class

Requirement: well-designed interface/API
- Trade-off between versatility and over-specialization
- Due to device class complexity, OS ends with layers of APIs
-> A good API must be constructed!!

<u>OS device structure :</u> 
![[Pasted image 20240329105619.png]]v

<u>Device driver structure</u>
Device drivers typically divided into two pieces:
1. **Top half**: accessed synchronously in call paths to initiate I/O
	 - system calls, eg: open/close/read/write
	 - by the filesystem
	 - By the networking stack
2. **Bottom half**: runs asynchronously on I/O completion 
	- As an interrupt routine
	- Communicates with the device
**Interaction between top and bottom halves are very tricky** 
(we wont see much more about device driver)

## Persistent storage

### Storage devices

<u>Magnetic disks</u> 
- Large capacity at low cost
- Block level random access
- Slow performance for random access
- Good performance for streaming access

<u>Flash memory</u>
- Capacity at intermediate cost
- Block level random access
- Medium performance for random writes
- Good performance otherwise

##### <u>MORE ON **Magnetic disk**</u>
![[Pasted image 20240329110207.png]]
Stores data magnetically on thin metallic film bonded to rotating disk of glass, ceramic, or aluminium. 
Terminology : 
	**Track**: concentric circles on the disk 
	**Sector**: slice of a track; smallest addressable unit 
	**Cylinder**: All the tracks under the head at a given point on all surfaces

Length of tracks vary across disk: outsidetracks have more sectors per track, and have higher bandwidth. 
Disk is organized into regions of tracks with the same number of sector/tracks
Disk has a sector-addressable address space (sectors: 512 or 4096 bytes)
Main operations: read/write
![[Pasted image 20240329110726.png]]

<u>Disk latency</u>
Disk latency = Seek time + Rotation time + transfer time

**Seek**: position the head/arm over the proper track
	Time to get the track :5-15ms (very big!)

**Rotational latency**: wait for the desired sector to rotate under read/write head
	Time : 4-8ms 
	Only need to wait for half of the rotation on average 

**Transfer time**: transfer a block of bits (sector)
	Time : 25-50 usec

To access/write this memory we have several policies : 

<u>Disk Scheduling</u>

**FIFO (first in first out)**
Schedule disk operations in the order they arrive. 

**Shortest seek time first** 
Select request with minimum seek time from current head position. 
A form of shortest job first (SJF) scheduling
Not optimal: suppose a cluster of requests at far end of disk --> starvation!

**Scan scheduling "elevator"** 
Move the head in one direction until all requests have been serviced, and then reverse. Sweeps disk back and forth. 
--> most optimal 

## File system performance 

<U>How do we define performance ?</u>
- The number of I/O operations
- The speed of I/O operations
- The impact on one program
- The impact on all programs

Key metrics : 
- Latency (of operations)
- Throughput
- I/O operations per second (IOPS)

<u>How do we improve performance?</u>
**1. Caching** 
Avoid unnecessary operations 

**2. Batching** 
Group operations to increase throughput (perhaps at the expense of latency)
Delay idempotent operations

**3. Add a level of indirection** 
Maintain some convenient abstraction 
Enable performance optimisation

<u>The block cache</u> 
What happens when the OS reads block of an inode over and over again ?
- A: the block may already be in the file system buffer cache (the block) cache:
- Map: {inode, block_offset} → page frame number
- sys_read can return without performing disk I/O

On modern systems, the file system buffer cache may use all unused memory. 

<u>Batching operations</u>
Each I/O operation is costly (latency), with limited concurrency (IOPS)
IDEA: 
- Perform fewer operations with larger transfers
- Possible when consecutive blocks on disk belong to the same inode
-> notion of "disk fragmentation" : a metric of what fraction of inode content are not on consecutive location on disk. 

<u>Delaying operation</u>
A process must block on a read operation. But what about a write ?
IDEA: Delay all write operations
- perform them asynchronously (typical: wait at most 30 seconds)
- Reorder operations to maximize throughput (insert within the elevator algorithm)
***!!!*** Consequence: content will be lost if the OS crashes

<u>Modern file system</u>
Today most of operation on memory are writes, because caches and caches algorithms have become *very very good*. 

So we use a **log-structured filesystem** (vrm aucune idée de ce que c'est mais voilà c'est là). 



