
# Monday Lecture : DNS & Security 

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

### Attacks against DNS
<u>impersonate a fake DNS server</u>
![[Pasted image 20240422102603.png]]
1. **Impersonation**: The attacker sets up a malicious DNS server or compromises an existing DNS server on the network.
    
2. **Injection of False Data**: The attacker sends forged DNS responses to the DNS resolver, claiming to be authoritative for a particular domain name. These responses contain incorrect mappings between domain names and IP addresses, effectively poisoning the DNS cache of the resolver.
    
3. **Caching of False Data**: The DNS resolver caches the forged DNS responses containing the incorrect mappings. Subsequent DNS queries for the affected domain name will be directed to the malicious IP address provided by the attacker.
    
4. **Redirection of Traffic**: Users attempting to access the legitimate domain name are redirected to the malicious IP address controlled by the attacker. This allows the attacker to intercept and manipulate the traffic, potentially leading to various malicious activities such as phishing, malware delivery, or interception of sensitive information.

<u>DoS the root and/or TLD DNS servers</u>
![[Pasted image 20240422102624.png]]
1. **Overwhelming Traffic**: The attacker floods the target DNS servers with a massive volume of traffic, far beyond their capacity to handle. This flood of requests consumes the server's resources such as bandwidth, CPU, and memory, making it unable to respond to legitimate DNS queries effectively.
    
2. **Exploiting Vulnerabilities**: The attacker exploits vulnerabilities in the DNS server software or operating system to crash or destabilize the servers. This could involve sending specially crafted packets or exploiting known weaknesses in the DNS protocol implementation.
    
3. **DNS Amplification**: The attacker harnesses the power of DNS amplification to magnify the volume of traffic directed at the target DNS servers. By sending small DNS queries with a forged source IP address to misconfigured DNS servers that respond with much larger DNS responses, the attacker can amplify the volume of traffic sent to the target, increasing the impact of the attack.
    
4. **Distributed DoS (DDoS)**: The attacker orchestrates a distributed denial of service (DDoS) attack by coordinating a network of compromised computers (botnet) to simultaneously send traffic to the target DNS servers. This makes it much more challenging to mitigate the attack since the traffic comes from multiple sources, making it difficult to filter out malicious requests.

<u>Trash the cache of a DNS server to slow down its response</u>
![[Pasted image 20240422102651.png]]
1. **Flooding with Invalid Queries**: The attacker floods the DNS server with a large volume of invalid or nonsensical DNS queries, overwhelming its cache with unnecessary data. These queries might be for nonexistent domain names or for domain names that the attacker controls.
    
2. **Injecting False Data**: The attacker injects forged DNS responses containing incorrect or malicious mappings into the DNS server's cache. These responses might claim to be authoritative for legitimate domain names and provide incorrect IP addresses, leading to cache pollution and slowing down the resolution process.
    
3. **Exploiting Cache Poisoning Vulnerabilities**: The attacker exploits vulnerabilities in the DNS server software or protocol to poison its cache with false data. This could involve sending specially crafted DNS responses containing malicious payloads that are cached by the server and subsequently used to respond to legitimate queries.
    
4. **Resource Exhaustion**: The attacker consumes the DNS server's resources, such as memory or CPU, by continuously sending large volumes of DNS queries or exploiting software vulnerabilities. This resource exhaustion can slow down the server's response time and make it less responsive to legitimate DNS queries.


### Alice and Bob
When Alice sends a message to Bob,only Alice and Bob can see the contents of the message. 

An eavesdropper Eve, sitting on the communication channel, cannot see the content. 

***First idea*** : sending the plaintext and a ciphertext, the ciphertext which is used to proove that the data send was from Alice. First problem (out of a million) we need to send ***x2*** the data, (text and ciphertext).   
![[Pasted image 20240422103541.png]]
We can solve this first problem by-->

***Second idea*** : sending the plaintext and a hashed ciphertext which is wellllll smaller thant the ciphertext. The hash function is know to Bob and Alice and is very hard to revert. 
![[Pasted image 20240422103959.png]]
	This is useful when : An impersonator Persa want to send a message (or tamper with Alice’s message) and convince Bob that it is from Alice

***Third idea*** : Same as the second but we make the hash from the plaintext
![[Pasted image 20240422104618.png]]

This is good for not *impersonate* someone, but the plaintext also need to be encrypted -->
#### Providing confidentiality 
***GOOD OLD RSA***
With <u>symmetric key</u> crypto 
	Alice encrypts with **shared key**
	Bob decrypts with **shared key**
	--> FASTER

With <u>asymmetric key</u> crypto
	Alice encrypts with Bob’s **public key**
	Bob decrypts with his **private key**
	--> NOT SHARED SECRET


<u>Proving authenticity and data integrity</u>
With symmetric key crypto:
	Alice appends to her message a MAC that can only be computed using the shared key
With asymmetric key crypto
	Alice appends a digital signature that can only be computed using her private key
	Bob verifies using Alice’s public key


<u>Public-key certificate</u>
Rely on certificate authority (CA)
	an entity that both Alice and Bob trust

CA generates public-key certificates
	Alice owns Alice-key+
	Bob owns Bob-key+
Ca digitally signs each certificate
	with its private key

Alice and Bob must know CA’s public key. They obtain that out of band. And it is typically stored within the web browser. 

<u>Authentication</u>
Client makes sure it has established a TCP connection with the intended server.

Server sends its public-key certificate
	signed by a trusted CA, with CA’s private key

Client verifies certificate signature
	using CA’s public key
![[Pasted image 20240422110257.png]]

------------------------------------------------------------------- 

# Wednesday Lecture :  Wrapping up the application layer 
## Network system calls 

### <u>Structure of a typical internet packet</u>
![[Pasted image 20240425153601.png]]
This is the same structure that we can observe by using *wireshark*. 
	At first we have the message 
		Then we add the transport layer (**focus of today lecture**)
			Then we add the network layer 
The message/transport is called a **segment**
The messafe/transport/network is called a **datagram**

#### <u>UDP sockets</u>
Each UDP socket has a unique (IP address, port #) tuple.
A process may use the same UDP socket to communicate with many remote processes. 
<u>Example sending</u>
![[Pasted image 20240425160324.png]]
- We first need to make a "socket" *syscall*, ask the transport layer to open a UDP socket. This socket is automatically linked with the process. 
- Then we need to "bind" with a particular local IP address and port number. 
- After we can do a "sendto" *syscall*, and provide as argument **a pointer to the message**, **length** and **destination IP address** 
- If we don't need this socket anymore, we make the "close" *syscall* 



#### <u>TCP sockets</u>
Listening & connection sockets.
Each connection socket has a unique (local IP, local port, remote IP, remote port) tuple.
A process must use a different TCP connection socket per remote process.