# Monday Lecture : Forwarding and IP

## Network-layer functions
![[Pasted image 20240506095725.png]]
### Forwarding
Local operation that takes place at a router and determines output link for each packet

**How**: read value from packet’s network-layer header, search forwarding table for output link
### Routing 
![[Pasted image 20240506095753.png]]
Network-wide operation that populates forwarding tables

**How**: routing algorithm run on a logically centralised network controller or the routers themselves
![[Pasted image 20240506095805.png]]


<u>Forwarding vs. routing</u>
- Forwarding determines output link for each packet
- Routing populates forwarding tables



## Network-layer types
### Circuit-switched network
- Uses circuits = network-layer connections
- Appropriate for performance guarantees
- Forwarding state : per connection 
### Connection & state 
Many different kinds of connections
- transport-layer (TCP) connection
- network-layer connections 
Connection => per-connection state 
- at transport layer = at end-system (OK)
- at network layer = at **all** the routers (not OK)
State => <u>complexity</u>
## IP forwarding 
### IP/ packet-switched network
![[Pasted image 20240506102830.png]]
Uses packet switching = no network-layer connections

Forwarding state: per destination prefix
	destination prefix, output link
	populated by routing

<u>Why packet switching?</U>
It makes forwarding tables *smaller*
	no per-connection state in routers
It makes routers *simpler*
	no support for connection setup and teardown in routers
	no risk of relevant attacks

### Location-dependent address 
***Problem*** if we need to many exception the forwarding table becomes huge
**Solution** : Location-dependent addresses

Address embeds **location** information (address proximity implies location proximity). Significantly reduces forwarding state

### IP prefix format 
IP address = number from 0 to 2^32 -1

IP prefix = range of IP addresses
![[Pasted image 20240506104946.png]]
With the **mask** IP address 223.1.1.74 /24 == 223.1.1.0/24 are the same

SUPER SCHEMA COMPLEXE
![[Pasted image 20240506105153.png]]0

### IP subnet 
Contiguous network area that does not “contain” any routers
All its end-systems and incident routers have IP addresses from the same IP prefix. 

### Broadcast IP address
The biggest IP address in an IP subnet

When a packet has this IP address as its destination IP address, it is destined to all the end-systems and routers in the IP subnet

E.g., if an IP subnet has IP prefix 223.1.1.0/24, its broadcast IP address is 223.1.1.255

### How to deal with the lack of IP address 
We only have 4 billions IP address (not enougth), so we need to find a way to connect more device to the internet. 
#### => Network Address Translation (NAT)
![[Pasted image 20240506105958.png]]
Requires per-connection state at the border routers of the private IP subnet

End-systems inside private IP subnets are unreachable from the outside world

