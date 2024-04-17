
# Videos 
## Pointeur et tableaux 
Les pointeurs et les tableaux sont quasi identiques.  (on peut faire les accès `ptr[2]`)
`int[]` est presque la même chose que `int* const` 

Mais **attention** les doubles tableaux ne s'implémentent pas comme des pointeurs de pointeurs. + les pointeurs peuvent pointer vers des espaces de taille différente 

## Pointeur arithmetiques
On peut faire des opérations avec les pointeurs, par exemples ++, +, -, +=
Ce qui nous permets de faire ce genre d'opérations: 
```C
int tab[N];

const int* const end = tab + N;
for(int* p = tab; p < end; ++p){ .... *p .....}
```

Que veut dire `*p++` ? 
--> C'est équivalent à `*(p++)` donc on prends la valeur en p puis par la suite on fait +=1 (rappel la diff entre ++p et p++ !!!)

Faire +1, **ça veut dire passer à l'objet suivant,** on a pas besoin de calculer la valeur en byte de chaque objet. 
## SizeOf
`sizeof(cequetuveux)` ***!!*** si on met une expression dedans elle est pas évaluées 

## Flexible array member
moyen encore plus complexe de faire des array......

# Monday Lecture

## Memory virtualization mechanism : Paging

<u>Recall : The goal for programs sharing physical memory</u>
- Transparency
	- Multiple programs coexist in memory and are unaware
	- Processes should be agnostic of the use physical memory
- Protection 
	- OS/other process should not corrupt each other
- Efficiency 
	- Performance should not degrade even when sharing memory
	- Process should not waste memory resources 

### <u>Key idea: page-based MMU</u>

We want to divide the address space into fixed-size chunks --> **pages** 
The mirror physical memory fixed-same-size chunks are called --> **frames**
An address space is a **map:{page--> frame}** 

This gives us the ability of giving non contiguous physical memory and make the program believe that it has contiguous memory allocated for it.

<u>Size of Pages</u>
The page is the minimal unit of an address space. 
It is currently between (4KB and 16KB), the smaller it is the better we can minimize internal fragmentation. 

<u>Address representation</u>

![[Pasted image 20240311094140.png]]

### <u>Address translation</u>
MMU translates from virtual address to physical address 
- Note : size of virtual and physical address may be different
![[Pasted image 20240311094645.png]]

To access a byte we need to go deeper: 
	Extract the virtual page number 
	Map the virtual page number into a frame number using a page table 
	Extract the offset 
	Convert to physical memory location : access byte at the offset in the frame 

### <u>Page table</u>
The page table is a table which store ALL **the virtual-to-physical address translation**

- Every process has one page table 
- The page table resides in memory and managed by the OS 
- The pointer to the page is stored in a special register (PTBR or /%cr3)

<u>The data in a page table entry is:</u> 
![[Pasted image 20240311101827.png]]
![[Pasted image 20240311101843.png]]

### Linear page table
The easiest way to implement 
- MMU indexes the array by the virtual page number and look up the page table entry at the index to find the physical frame number. 
- Linear page table can use multiples of pages to maintain the mapping 
![[Pasted image 20240311102223.png]]
***BUT*** this system is quickly very big !! and looking for a page entry become super long
(bref c'est pas super efficace mais assez logiquement quoi)

### Multi-level page tables
Add levels of indirection to efficient encoding 
**Analogy**: Library section → aisle number → shelf number → book position

Exemple : le cours de comparch

<U>Paging pros and cons</U>
Advantages: 
	No external fragmentation 
	Fast to allocate and free
Cons : 
	Some space required for storing the page table information 
	Additional memory ref to page table as it is stored in memory 
	Requires complex hardware for efficiency --> TLB


## Translation lookaside buffer (TLB)
Adding a cache to our paging system boost the efficiency a lot! 
--> We first look into the TLB if it is not in there, we go search into the page table 

It the reality (today) **99%** of the access are hit, because a **miss** is very very **expensive** (in time)

# Wednesday Lecture

## What is a file system?
What is <u>the purpose</u> of a file system?
	Manage the block of memory given by the storage devise **efficiently** 
	. 
	Manages data on nonvolatile storage
	Enables users to **name** and **manipulate** semi-permanent files 
	Provide **mechanisms** to organize files and metadata-owner, permission or type
	Share files among users and processes 
	Restrict access to files to certain users 

### General IO abstraction stack 
IO systems are accessed through a series of layered abstractions: 
![[Pasted image 20240313105914.png]]
- Block cache :
	- caches blocks recently read from disks
	- Buffers recently read blocks 
- Block device interface: 
	- Single interface to many devices
	- Allows data red/written in fixed size blocks
- Device driver : 
	- Translates OS abstractions and hw specific IO device details 
- MM IO, DMA, interrupts 
	- Controls registers, bulk data transfers, OS notifications

Also the <u>library</u> provide an API to facilitate access to element or read etc.. 
- examples : `fopen` `fread` `fwrite` `fseek` 
- Operate on `FILE*` streams 
- It also have additional buffering options 

If we do not use the library in C we can use (just below in the library) <U>the linux calls</U>
- like `open` `read` `write` `lseek` (remark only the f in front is not there)
- It operates on file descriptors 

## File system abstraction 
The abstraction is composed of to main components :
- **Files**
- **Directories**
Which is the ***answers*** to this problems: 
	Store **large** amounts of information 
	Do it in a way that **outlives** the program
	Can support **concurrent** **accesses** from multiple processes 
	Present applications with <U>a persistent data</u>, and <u>named data</u>

### The File 
A file is named collection of related information that is recorded in secondary storage. 
Or a linear persistent array of bytes 
We can divide the file in 2 parts:
1. **Data** : what a user or an application puts in it
2. **Metadata** : information added and managed by the OS
	- Size, owner, security info, modification time....

<u>The file is seen differently by whom watch it</u> 
- OS view: the persistant ID, called the <u>inode</u> and device number
- Human : a path
- Process view : file descriptor 

#### Inode

<u>What is it?</u>
It's a low-level **unique** ID assigned to the file by the file system (each file has exactly one inode)
	Note : Inodes are unique for a file system but not globally
	+They are recycled after deletion
It contains metadata of a file 
	Soooo --> permissions, length, access times
	Location of data blocks and indirection blocks

<u>Inode table</u>
![[Pasted image 20240313120052.png]]
Storage space is split into inode table and data storage
File are statically allocated (dont know what it means)
Require inode number to access file content (logic)

**Idea**: Use a dedicated place at the beginning of the storage media, mostly initial block

#### Path 
Each file has a human readable format: **file name** (we remember name better than numbers)

Files are organized into hierarchies of directories: **pathame** (We like to organize things logically)

Modern file systems mostly use untyped files: **array of bytes**
- File is a sequence of bytes 
- OS/file system does neither **understand** nor **care** about contents

<u>Path --> inode</u>
A special file (directory) stores the mapping between those

Extend to hierarchy: Mark if a file maps to a regular file

<u>Inodes and Directories</u>
Inode does **NOT** contain the file name
**Each directory is a file** (stored like regular files)
	Flag in the Inode separates directories from regular files
	Flag restricts API to processes (e.g., cannot write to a directory)
	Contains array of { filename, Indode}
Multiple file names can map to the same inode
	→ inode has a reference count
	Called **shortcut** in Windows, or hard link in UNIX/Linux

<u>Directory</u>
A special file that stores the mapping between human-friendly names of files and their inode numbers
Contains subdirectories:
	List of directories, files
	/ indicates the root (typically inode:1)

#### File descriptor 
The combination of file names and inode/device IDs are sufficient to implement persistent storage. 

***BUT*** 
**Drawback**: constant lookups from file names to inode/device IDs are **costly**
So the **idea**: do expensive tree traversal once, store final inode/device number in a per-process table. 
	Also keeps additional information such as file offset
	Per process table of open files 
	Use linear numbers (fd 0, 1, 2 …); reuse when freed

Example : 
![[Pasted image 20240313125858.png]]
## File system APIs

<u>Create a file</u> :
![[Pasted image 20240313130007.png]]
this returns a **file descriptor (fd)**, that grants the capability to perform certain operation on the file. 

<u>Close a file</u>
![[Pasted image 20240313131922.png]]

<u>Reading/writing to a file</u>
	Reading
	![[Pasted image 20240313132023.png]]
	Writing 
	![[Pasted image 20240313132126.png]]
	. 
	Returns the number of bytes read/written

<u>Manage file offset</u>
![[Pasted image 20240313132313.png]]
Re-positions file offset
Initially, it is set to 0, which is updated on read/write
	To offset bytes from the beginning of the file ( SEEK_SET) 
	To offset bytes from current location (SEEK_CUR)
	To offset bytes after the end of the file (SEEK_END)

<u>Unlink/delete a file</u>
![[Pasted image 20240313132837.png]]
Removes a file from a directory (when the reference count is 0)
Decreases the reference count of the file

<u>Synchronous write</u> 
![[Pasted image 20240313133206.png]]
Flushes all dirty data to disk for a file referred by fd. 

<u>Get file metadata</u>
![[Pasted image 20240313141941.png]]
Writes the information about the inode metadata in the statbuf struct 
	Device ID, inode number, number of hardlink, user ID
Most information is retrived from the file's inode 
	+Inode can be also cached in memory 

## File system implementation 

<u>Implementation</u> 
File system manages data for users
**Given**: a large set (N) of blocks
**Need**: data structures to encode file hierarchy and per file metadata
- Overhead (metadata vs file data size) should be low
- Internal fragmentation should be low
- Efficient access of file contents: external fragmentation, # metadata access
- Implement file system APIs
Several choices are available (similar to virtual memory)

<u>Layout</u>
File system is stored on disks
	Disk can be divided into one or more partitions 
	Sector 0 of disk: master boot record (MBR), which contains:
		- Bootstrap code (loaded and executed by the firmware)
		- Partition table (addresses of where partition start and end)
	First block of each partition has a boot block
		- Loaded by executing code in MBR and executed on boot
![[Pasted image 20240313144040.png]]

<u>What is inside a partition??</U>
Persistent storage modeled as a sequence of N blocks. 
	From 0 to N-1: 64 blocks, each of 4KB
	Some blocks store data
	Other blocks store metadata:
	- An array of inodes
		- At 256 bytes, 16 per block: with 5 blocks for inodes, file system can have up to 80 files
	- Bitmap tracking free inodes and data blocks (free lists)
	- Boot block and superblock are at the beginning of the partition
	- Superblock 
		- store : # of inodes, # of data blocks, where the node table begins, may contains information to manage free inodes/data blocks 
		- is read first when mounting a file system 

![[Pasted image 20240313144853.png]]
### File allocation 
We got various ways to allocate data to files: 
- **Contiguous allocation**: All bytes together, in order
- **Linked structure:** Blocks ends with the next *pointer*  
- **File allocation table:** Table that contains block references
- **Multi-level indexed:** Tree of pointers 

<u>Contiguous allocation</u> 
Quite self-explanatory 
The **Good**
- Simple : Only need start block and size
- Efficient : One seek to read an entire file
The **Bad**
- Fragmentation: external fragmentation (can be serious)
- Usability : User needs to know file’s size at the time of creatio

Great for **read-only file** systems (CD/DVD/BlueRay)

<u>Linked blocks</u>
![[Pasted image 20240313173342.png]]
Each file consists of a linked list of blocks
Usually first word of each block points to the next block & the rest of the block is data

The **Good**
- Space utilization: No external fragmentation 
- Simple : Only need to find the first block of a file
The **Bad** 
- Performance: Random access is slow → high seek cost (on the disk)
- Implementation: Blocks mix data and metadata
- Overhead: One pointer per block metadata is required

<u>File allocation table (FAT)</u>
Same as linked block but, decouple data and metadata: Keep linked list information in a single table. 
![[Pasted image 20240313173834.png]]
The **Good**
- Space utilization: No external fragmentation
- Simple: Only need to find the first block of a file
The **Bad**
- Performance: Poor random access
- Overhead: Limited metadata
	- Many file seeks unless entire FAT is stored in memory:


<u>Multi-level indexing</u> 
Have a mix of direct, indirect, double direct, and triple indirect pointers for data
C'est une singerie, mais du coup: 
![[Pasted image 20240313174903.png]]
Each node can store/point to (with this three layers) up to 4 TB of storage. 
Then the image is quite self-explanatory 

The **Good**
- Space utilization: No external fragmentation
	- No conflating data and metadata in the same block
- Simple: Only need to find the first block of a file
- Performance: Reasonable read and low seek cost
The **Bad**
- Overhead: Low metadata overhead but requires extra reads for indirect/double indirect access