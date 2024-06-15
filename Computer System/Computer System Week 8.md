
# Wednesday Lecture : The Web

## Designing an application 

### Design the architecture

<u>The client/server architecture</u>
![[Pasted image 20240417083229.png]]
Clear separation of roles 
	Client processes make requests
	Server processes respond
Servers typically run on dedicated infrastructure 
	Used to be one or two computer 
	Now it is often a set of data-centers

We have 2 cool transport protocol : 
**UDP : User Datagram Protocol** 
![[Pasted image 20240417084657.png]]
UDP is connectionless or stateless
	does not keep state (sends and forgets)

**TCP : Transmission Control Protocol**
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

<u>The Web</u>
![[Pasted image 20240417091808.png]]
### Design the communication protocol
WHO AM I
WHY AM I HERE

<u>The HTTP protocol</u>
Request: GET, HEAD, POST ...

Responses : OK, Not found, Moved permanently, Bad request... 

The web client sends an HTTP request; the web server sends an HTTP response (logic)

#### <u>Web Page</u>
A base file + referenced files: 
	base files specifies structure
	referenced files may be image, videos, scripts (to get all the content for the page)
Each file has it own URL
	URL = name/address for web resources
	e.g. htpps://actu.epfl.ch/image/142932/1929x1080.jpg

#### <u>Cookies</u>
When we go to INTERNET, we are not just sending request and receiving responses, the INTERNET store cookies (miam-miam). These cookies are information stores about us to facilitate our browsing in the web (keep connexion logs / preference info)

![[Pasted image 20240417093144.png]]
Here when looking for recipes, the server is sending him back recipes about GREECE (because he has look for information about greece before). 

So the cookies is a state created by the server, stored on the web client 
It links subsequent HTTP requests to the same web client 

Now we use "*cookie-less tracking*" which is the same but it is stored on the **server side** rather than the client side  

### Choose the transport: TCP or UDP?

#### TCP 
With the TCP protocol you must first make a request (without any data) to ask if the server is going to respond (so we have to do a "empty" roundtrip with the server before communicating with the server). This creates a delay... 

We call the minimum delay to get a web page the RTT: round-trip time from client to server 
	It is the time for a small packet to get from the client to server and back 

SO TCP has a performance cost, but this is to assure a response from server (we trade efficency for certainty)

We have some typical ways to use TCP : 
<u>Persisitent TCP connections</u>
	Reuse the same connection for multiple HTTP requests/responses between the same client/server

<u>Parallel TCP connections</u>
	One client/server pair establishes multiples TCP connections at the same time 
	(no super efficaces (selon Eddy))

#### Proxy
We also have something called a <u>proxy</u> which is used to make the connection between you and the server, this proxy has already "ask" so we can directly access data !
-->The proxy caches content hosted by other web servers
![[Pasted image 20240417100107.png]]
Problem : the information on the proxy can be expired, not up to date. So we need to refresh the proxy every hour or so (depending on the content of the server)

To ensure fresh data : 
	An origin web server may assign an *expiration date* or *max caching* age to each URL. 
	A web client or proxy web server may send **a conditional HTTP GET** request for a URL
		using the if-modified-since field in the HTTP header

#### Caching 
Is an universal technique for improving performance

The challenge is to ensure fresh data!

Same solution as for the proxy !! (see more next week)