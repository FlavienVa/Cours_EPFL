
# DNS & Security 

## Vocabulary 

<u>Application Porgramming Interface</u> 
Syscall API : Interface between application programmer and OS for doing "privileged stuff"
Accessing peripheral devices 

<u>Network interface</u>
Interface between an end system and the network 
A piece of hardware or software that sends and receives packets 
E.g the network card of your laptop


<u>DNS name</u> 
Identifies a network interface = identifies an end-system
Also called a hostname 
	an end-system is also called a host 

<u>URL</u>
Identifies a web resource/object
	e.g. epfl.ch/labs/nal/pub
Format: DNS name + file name
	epfl.ch identifies a network interface
	 /labs/nal/pub identifies a file

## The domain name system (DNS)

DNS stands for Domain Name System. It's essentially the phonebook of the internet. When you type a domain name (like [www.example.com](http://www.example.com)) into your web browser, DNS translates that human-readable domain name into an IP address, which is the unique numerical identifier for the website's server on the internet
![[Pasted image 20240422092841.png]]

### What architecture uses DNS?
This a very very very used system, all request on the internet need it, so we need to have **the ability to grow** without having a **big cost**. 

What is the best architecture for that?

The base architecture: 
![[Pasted image 20240422093540.png]]

Architecture that group by .ch (and .com/.org etc)
![[Pasted image 20240422093730.png]]

These structures requiere that we have a **hierarchy** and that the top level DNS servers know the low levels DNS servers. 
	Information organized in one or more trees 
	Each node interacts with (and keeps state about) its children and it parents
	Universal technique for scalability

If this is not the case we need to ask to **all** DNS servers
![[Pasted image 20240422094623.png]]

To ensure **freshness** of the data we need to update every X time :
![[Pasted image 20240422095031.png]]
(TTL being Time To Live)

**Caching** 
	Universal technique for improving performance
	Challenge: ensure fresh data
	Solution: expiration data or max caching age + dynamic check
### What protocol do DNS use
Query : request for an RR (Ressource Record)
Response: answer to query
A DNS client or server sends a query; a DNS server sends a responses

<u>Resource Record</u>
	mapping, e.g., DNS name → IP address
	many types: A, AAA, CNAME, MX, SOA, …

### What transport do DNS use (TCP or UDP)

UDP for short exchanges
	typically between DNS client and server
	does not makes sense to pay the performance penalty for TCP connection
TCP for longer exchanges 
	Between DNS servers


## Security 

## 