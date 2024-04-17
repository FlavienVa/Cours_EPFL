
# Wednesday Lecture : The Web

## Designing an application 

### Design the architecture

<u>The client/server architecture</u>
![[Pasted image 20240417083229.png]]
Clear separation of roles 
	Client processes make requests
	Server processes respond
Servers typically tun on dedicated infrastructure 
	Used to be one or two computer 
	Now it is often a set of data-centers

We have 2 cool transport protocol : 
**UDP**
![[Pasted image 20240417084657.png]]
UDP is connectionless or stateless
	does not keep state (sends and forgets)

**TCP**
![[Pasted image 20240417084720.png]]
TCP is connection-oriented or stateful
	keeps state on the ongoing communication 

<u>Transport-layer technologie + services</u>
- (some) reliability
- NO performance guarantees
	- no upper bound on loss or delay
	- no lower bound on throughput 
- We say that TCP offers "reliable data delivery"

<u>What about security?</u>
![[Pasted image 20240417085104.png]]
In the form of a library
Security-related functions 
	encryption, authentication, ...
Security-related protocols (combine all together)
	TLS (Transport Layer Security)
	SLL (Secure Sockets Layer)


### Design the communication protocol

### Choose the transport: TCP or UDP?