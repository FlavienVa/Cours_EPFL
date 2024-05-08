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

# Wednesday Lecture  : Routing 

## Recall
### Forwarding 
**Local** operation that takes place at a router and determines output link for each packet
**How**: read value from packet’s network-layer header, search forwarding table for output link
### Routing 
**Network-wide** operation that populates forwarding tables
**How**: routing algorithm run on a logically centralised network controller or the routers themselves
### Internet (IP) forwarding 
Uses packet switching = no network-layer connections
Appropriate for best-effort service
Forwarding state: per destination prefix
- destination prefix, output link
- populated by routing

<u>Forwarding vs. routing</u>
- Forwarding determines output link for each packet
- Routing **populates** forwarding tables


## Algorithm for Routing 

### Least-cost path routing 
Goal: find least-cost path from each source router to each destination router

#### <U>Link-state routing algorithm for source u</u>
![[Pasted image 20240508083858.png]]
1. Consider only the adjacent destinations 
2. Compute the routes from the closest link 
3. Repeate

**Input**: router graph & link costs
**Output**: least-cost path from source router u to every other router

“Centralized” algorithm: runs on a single entity
Option #1: Router u runs the algorithm
Option #2: Separate computer (“network controller”) runs the algorithm for all the routers
#### This is : Dijkstra's algorithm
At each step, consider a *new router*
- starting from “closest” neighbor
Check whether current paths can be improved
- by using that router as an intermediate point
End when no improvement is possible


#### <u>Distance-vector routing algorithm</u>
![[Pasted image 20240508090032.png]]
![[Pasted image 20240508090042.png]]
**Input to each router**: local link costs & neighbor messages
**Output of each router:** least-cost path to every other router

This is a "distributed" algorithm.
All routers run it "together" : neighbors exchange and react to each other's messages. 

#### This is : Bellman-Ford algorithm 
- All neighbors exchange information
- Each router checks whether it can improve current paths by leveraging the new information
- Ends when no improvement is possible

### Link-state vs distance-vector
<u>Link state:</u> each entity first obtains complete view of the network, then computes the least-cost paths.
-->It converge **faster**
<u>Distance vector:</u> each entity obtains incrementally new information about the network at every round.
-->It uses **less bandwidth**

### Internet routing challenges
Scale
- link-state would cause flooding
- distance-vector would not converge
- too many IP subnets => too many forwarding-table entries
Administrative autonomy
- an ISP may not want to do least-cost routing
- may want to hide its link costs from the world

## Autonomous System 
![[Pasted image 20240508093722.png]]
Every autonomous system can run it own algorithm (Dijkstra, Bellman or other)

![[Pasted image 20240508094153.png]]
A link in a autonomous system know the *exact* destination of his (bro) co-link in the same system, but it only knows more *simple* information about all the other destination of the other autonomous system. 

![[Pasted image 20240508094728.png]]
We also have "border routers" that transmit the information about where to find the information (here 5.0.0.0/16)

![[Pasted image 20240508094838.png]]
In the AS the border router "represents" the connection to the outerworld 

#### Internet routing 
Each router learns one route to each IP subnet in local AS
Each router learns one or a few routes to each foreign AS
	But not one route to each IP subnet of each foreign AS

#### Intra-AS routing 
Run by **all the router in the same AS** 
<u>Goal</u> : propagate routes within local AS
- each router advertizes routes to its local IP subnets
- and potentially routes to other ASes that it has learned trough BGP

#### Inter-AS routing 
Run by **all *border* router** between ASes 
<u>Goal</u> : propagate routes outside local AS
	each border router talks to external neighbors (eBGP)
	and to the other border routers of the local AS (iBGP)
**BGP** = Border Gateway Protocol (variant of the bellma-ford algorithm)


#### Internet routing challenge 
<u>Scale</u>
- link-state would cause flooding
- distance-vector would not converge
- too many IP subnets => too many forwarding-table entries
<u>Administrative autonomy</u>
- an ISP may not want to do least-cost routing
- may want to hide its link costs from the world

#### => Solution : hierarchy 
Each router communicates with every **local** router
Each border router communicates with every **external** neighbor
Each router keeps forwarding state for local IP subnets and foreign aggregate IP prefixes
Each AS chooses its ***own* intra-AS routing protocol**
