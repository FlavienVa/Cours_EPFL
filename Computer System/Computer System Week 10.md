# Monday Lecture : Transport layer and TCP

## UDP services 

<u>Corruption detection: Checksum</u>
![[Pasted image 20240429092327.png]]
This **checksum** is used to check (du coup) if the message has not been corrupted. It can be some redundant information, like *the binary sum of all data byte* 
If the sum is not correct the receiver knows that the message has been corrupted!

## TCP services 

### Reliable data delivery
Aka : try hard to get the data to the receiver. 

<u>Connection setup</u> 
**3-way handshake** 
	"TCP client”: end-system initiating the handshake
	"TCP server”: the other end-system
![[Pasted image 20240429092838.png]]
- The first 2 segments carry a SYN flag
- "TCP connection" = resources (sockets, buffers..) allocated for communication 
- When can also send  a **message** with the last ACK (since the connection is established)

<u>Connection teardown</u>
![[Pasted image 20240429093432.png]]
- Each side indicates that it is ready to close the connection 
- Send a segment that carries a FIN dlag 
- The connection is closed when both sides have sent a FIN and received and ACK

<u>Acknowledgment</u> 
![[Pasted image 20240429093627.png]]
- Feedback from receiver to sender
- Receiver adds ACK to each segment
- Sender uses it to detect and overcome corruption by retransmitting
	- If we get a NACK (negativ ACK), it retransmits the data

<u>Sequence number</u>
![[Pasted image 20240429094049.png]]
- An identifier for data
- Sender adds SEQ to each segment (byte)
- Receiver uses it to determine whether it is receiving a new segment or a retransmission. 

<u>Acknowledgment number</u>
![[Pasted image 20240429094449.png]]
- Receiver indicates the next byte it is expecting 
	- not the last byte it has received
- There are no explicit negative ACKs
	- ACK 0 means "I did not receive SEQ 1 correctly"
- LESS MECHANISM! 

<u>Timeout</u>
![[Pasted image 20240429095325.png]]
"here the ACK is so delay (but not lost) that it overpass the timeout time so we **resend**"
- No arrival of an expected ACK 
	- a segment was lost or delayed
	- the ACK for a segment was lost or delay
- Sender uses it to detect and overcome loss by transmitting 
	- if the sender times out, it retransmits

<U>Timeout calculation</u>
We need to add: 
1. RTT (Round Trip Time), we may do a "estimate RTT" for example 0.8 EstimatedRTT + 0.2 SampleRTT
2. The variance, DevRTT = function(RTT variance)
**Timeout = EstimatedRTT + 4 DecRTT**

We want te calculation to be : empirical and conservative

<u>Real example</u>
![[Pasted image 20240429100017.png]]
- A TCP connection may carry bidirectional communication
- SEQs and ACKs are always present, even if it appears unnecessary


<u>Macimum segment size</u>
![[Pasted image 20240429100607.png]]
- Transport layer may create **multiple segments** to send one message
- Dictated by the network properites
	- typically **1500 bytes**
### Flow control 
<u>Goal: not overwhelm the receiver</u>
	not send at a rate that the receiver cannot handle
How : **receiver window** 
	number of unacknoledged bytes that the sender can send without overwhelming the receiver 
	spare room in receiver's rx buffer
	receiver communicates it to sender as TCP header field
![[Pasted image 20240429102926.png]]
Here the receiver send that it has 0 space, but to know if the sender can send data again, the sender send periodically "empty" message just to know if there is free space. 

### Congestion control 
<u>Goal: not to overwhelm the network</u>
	not send at a rate that the would create network congestion
How : **congestion window** 
	number of unacknowledged bytes that the sender can send without creating congestion
	sender estimates it on its own

<u>Self-clocking</u>
Sender **guesses** the "right" congestion 
ACK = no congestion, increase window 
No ACK = congestion, decrease window

So we just send **more** and **more** data, and see if the server respond !

for that we are going to : 
<u>Increase window size</u>
In two way : 
1. **Exponentially** 
	By 1 MSS  (Maximum Segment Size) for every ACKed segment 
	= window **double every RTT**
	when we do not expect congestion 
2. **Linearly** 
	by MSS* MSS/N for every ACKed segment 
	= by **1 MSS every RTT** 
	when we expect congestion

So we increase first exponentially and when we don't get response, we increase linearly. This is called the Tahoe algorithm : 
- Set window to 1 MSS, increase exponentially
- On timeout, reset window to 1 MSS, set ssthresh to last window/2, retransmit
- On reaching ssthresh, transition to linear increase

### TCP terminology 
Exponential increase = slow start 
	it’s called slow, because it starts from a small window; but it’s not really slow, the window increases exponentially

Linear increase = congestion avoidance
	this term does make sense; it means that TCP expects congestion, so it increases the windowmore cautiously

### Flow + congestion control 
<u>Goal: not overwhelm receiver or network</u>
How : **sender window** 
	sender learns receiver window from receiver
	sender computes congestion window on its own
	Sender window = min{ receiver w, congestion w }


## A bit of security

### Connection hijacking
Attacker **impersonates** TCP server (or client)
	Sends segment that appears to be coming from the impersonated end-system 
**Approach** : fake valid segment 
	id the TCP header perdictable 
**Solution** : make TCP header (SEQs) **unperdictable** 
![[Pasted image 20240429105833.png]]

### SYN flooding
![[Pasted image 20240429110138.png]]
Attacker exhausts buffer for incomplete connections 
	sends lots of connection setup request
Problem: **one small resource** affects all TCP communication
Solution: remove the resource
	pass the state to the TCP client, remove the buffer !!
![[Pasted image 20240429110655.png]]
