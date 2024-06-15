# Wednesday Lecture : Virtualization 
## Virtualization Fundamentals

<u>Principle of indirection</u> (again)
"Any problem in computer science can be solved by adding a layer of indirection"

Virtualization applies indirection to layering when the layered abstraction is compatible with the layer it relies upon

<u><b>3 main virtualization mechanisms</b></u>
-Virtualization by *mutiplexing*
	Expose a single resource among multiple virtual entities
	Paging etc..
-Virtualization by *aggregation*
	Make multiple resources appear as one single virtual resource
	Aggregate mutiple disk into *one*
-Virtualization by *emulation*
	Make a physical resource appear as another physical resources
	Run a old program in a new machine (DS emulator like julie)

<u>Virtual memory</u> (review)
Isolation & context switching (process abstraction)
- Enforced modularity across address spaces
- OS changes the page table base (privileged instruction)
- MMU is flushed
Demand paging
- OS page fault handler changes PTE and reloads MMU 
Copy on write, the foundation of fork()
- OS page fault handler (i) allocates new physical page, (ii) copies the content, and (iii) makes PTE(s) writeable
## Aggregation & Emulation 
### Many appear as one
#### Primary use : aggregating physical resources 

A computer has multiple DIMM (Dual Inline Memory Module / RAM)
- The memory controller aggregates the physical memory into a single (typically contiguous) namespace

A computer has many hard drives
- RAID (Redundant Array of Inexpensive Disks) make them appear as a single volume

A network has multiple links connecting servers
- Link aggregation and multipathing create a virtual link with more bandwidth

#### RAID 
**RAID** or "**redundant array of independent disks**" is a data storage virtualization technology that combines multiple physical disk drive components into one or more logical units for the purposes of data redundancy. 
![[Pasted image 20240522085113.png]]
M = aggregated memory 
N = sector in the disk
n = nbr of disk

### Emulation 
Idea : **use software** to emulate a resource different from the underlying resource. 
Because the physical *X*(CPU, Memory,..) does not exist or exist in a different architecture (ARM vs x86)

## Virtual machines
![[Pasted image 20240522091822.png]]
Apply virtualization to an entire computer system 

<u>Why virtual machine</u>
Very popular in the very early days of computing (60’s, 70’s)
-  Hardware was expensive
- Operating systems were primitive (pre-MULTICS)
Back in favor since 2000
- To ensure compatibility with the OS of choice
- To consolidate servers
- As a foundation for multi-tenancy (different owners)
- Because it is easy to provision a VM than a real machine
- …. And therefore, as the foundation of the cloud

<u>Definition</u>
"A virtual machine is an efficient, isolated duplicate of the real machine"

The virtual machine monitor (or hypervisor) VMM
=> Is always in complete control of the system
=>Uses the hardware directly so that programs show at most a minor decrease in speed

<u>Terminology</u>
Operating systems
- **Guest operating system** : running inside a virtual machine
- **Host operating system:** running directly on the hardware - combined with the VMM

Memory
- Virtual memory -- as commonly understood
- **Guest physical memory** -- physical memory of the VM
- **Host physical memory** -- (actual) physical memory

### VMM (virtual machine monitor) Construction

VMM multiplex **and** emulate

Multiplex CPU and memory, with specific hardware support
- Each VM has its own virtual CPUs (=virtual core)
- Each VM has its own guest physical memory
Emulate I/O devices
- Virtual disk, virtual keyboard, virtual screen, virtual NIC, virtual Ethernet switch, etc

<u>Limited direct execution</u>
![[Pasted image 20240522103332.png]]
Key observations : 
- Virtual CPU == virtualize HW/SW interface (e.g. x86-64)
- Standard virtualization trick -- expose the same abstraction V as the underlying resource P and everything else will still work

<u>Virtual CPU</u>
Multiplexed in space and time -- very much like kernel threads
- Space: VMM schedules vCPU from different VMs on different core
- Time: VMM schedules multiple vCPUs on a limited set of physical cores
Isolation guaranteed via:
- Non-root mode execution of the VM
- Host OS / VMM is presumed to be correct (very much like the OS)
Basic mechanism : limited direct execution
- Same idea as with protected operating system
- Extended here from application-only to system-level

<u>Virtualizing physical memory</u> 
We use extended (or nested) page tables 
![[Pasted image 20240522104121.png]]
Hardware simultaneously walks through **two independent page table structures**
- One tree, controlled by the guest, containing GPA
- One tree, controlled by the VMM, containing HPA
- Simultaneous walk requires **quadratic number of references**
- Implemented in hardware, with dedicated caches to improve performance

But with TLB we do not lose a lot of performance 

