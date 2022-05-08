# System Design

## Distributed systems Principles

- Single responsibility principle
- No single point of failure
- No bottle-neck principle

### Steps to System Design
---
- Requirement analysis : what need to be done/priority, security etc. We need to check what is the functional (What should the system do?) and non-functional requirements (how the system should behave?)
- API design for all functional design
- Define the data model (associations between the models and what attribute the model should have)
- High level design : Dataflow from end-to-end, database design, security etc
- Scale the design

## Scaling

* Vertical Scaling : 
	- upgrade hardware/software of the existing system adding more memory, compute, storage
	- Load balancer is not necessary
	- Single point of failure
	- Data is consistent
	- Limit on scaling vertically

* Horizontal Scaling: 
	- Increase the number of instances
	- Might need a LB & is resilient to failures
	- Data may be inconsistent
	- Highly scalable

## Load Balancer

It can be deployed as hardware or software in active-active or active-passive mode to prevent single point of failure at LB. It deligates the requests from the client to the appropriate servers and help to balance the load across the servers without client knowing which server is serving the requests.

* Algoritmss use for load balancing purpose:
	
	- Round Robin
	- Least Loaded : LB sends a request to the server and based on the response time the LB decides the load
	- Session Based : LB needs to maintain a sticky session to route the requests to the same server
	- Hashing : Requests are hashed on some criteria and the request is send to that server. To prevent routing to the server which is down, we can use consistent hashing.


## Caching

Improves the read performance. 80:20 rule states 20% of data is accessed 80% of the time. 

* Cache Invalidation

	- Write-through cache: Data written to the storage and cache at the same time.
	- Write around cache: Data written to storage and mark the cache as invalid. On second query for the value of invalid data, the recent results are pulled from storage and send to client/stored in cache
	- Write-back Cache: Data is written in cache & success response is sent to the client; and later in the storage. Disadvantages: if the cache crashes then the data is lost.

* Cache Eviction:

	- Least Recently Used (LRU) 
	- Most Recently Used (MRU)
	- First in first out (FIFO)
	- Last in first out (LIFO)
	- Least Frequently Used (LFU)
	- Randome Replacement (RR)

* Distributed Cache

	We should maintain the mapping on the data which has been sharded across multiple caches. This can be achieved using a hashing. However, it is scalable, and can be maintained on a cheap servers. If one cache goes down it can be replaced with another one and consistent hashing can be used to partition the key across multiple instace of cache.


## Consistent Hashing

The hash functions generate the hash in a certain range say 0-100. The total range is then divided into multiple sub-range say 0-25; 25-50; 50-75; 75-100. Each nodes store a separate range of key. However, if one of the server goes down or new server is added, there is an inconsistency in key range distribution. Some server would just handle lower range of key and some might be burdened with large range. This is BAD in terms of distributed systems design.

Solution [Consistent Hashing] : The replica of the same servers is added which shares the key load. If a new server is added or removed the key range is then shared across to the next server replica so that all the servers are balanced. 

It helps to solve the following problems in distributed systems:
	
- helps with elastic scaling for cache servers
- scale out a set of storage nodes 


## Storage

* Object Storage
	- Take that file, attached metadata (e.g., security attributes, content type, size, etc.), and give it an identifier
* Block Storage
	- Take that file, break it into pieces (aka "blocks"), and store that in a convenient way (i.e., convenient from the point of view of an operating system)
* File Storage
	- data stored in a single piece of information (e.g., if you save this presentation as a PDF on your computer then we call that PDF a file)

##### RAID (Redundant Array of Independent Disks) 
---
It is a way of storing the data on multiple disks or SSD to protect it in case of failure. There is a RAID controller which controlls all the read/write operations; data replications; improving performance and protecting the data on multiple devices.

RAID Levels provide different confirgurations. The standards are RAID 0 - RAID 5, RAID 6 modification of RAID 5 and RAID 10 is combination of RAID 1 + RAID 0. The different types of RAID is divided into different options based on the following criteria:

- The data is striped (i.e splitted) among multiple disks.
- Data is mirrored among multiple disks
- Data paritiy; error correction code; checksum are maintained to make sure the missing data can be recovered from the existing data partitions & parity bits; use ECC to regenerate the data lost.
- To make things more redundant the parity bits are distributed across multiple disks and replicated to make sure the data recovery can be achieved.


## Database Concepts

#### CAP Theorem
---

Distributed System CAP Theorem. Out of the following three properties only two can be achieved.

* Consistency : The data is syncronized across multiple instance so users see same data at the same time
* Availability : Service is online all the time even with node failure
* Partition Tolerance : Service is up even with network failure between nodes

For CP system: If there is partition, and we need a consistent system then the system must be restarted to sync up the data across the instance which are now partitioned because of the network failure. This will take away the availability. Example: MongoDB, Hbase, Redis

AP System: Even on both the partitions the systems are available, the data CAN NOT be consistent. Since nodes are not connected the data can't be synced. Example: CouchDB, Cassandra, DynamoDB

CA System: System is available and consistent then we can not add Partition tolerance as the data won't be able to replicate across instance. Example: RDBMS


#### ACID Properties
---

* Atomicity : no partial success
* Consistency : data consistent
* Isolation : multiple transactions shouldn't intermix and create incorrect results. Each transaction works on isolated dataset
* Durability: Change should be persistent even with hardware failure


##### Database Replication
---

* Strong Consistency : Request is written to all instance and then the success response generated.
* Eventual Consistency : Data is replicated later after the response is sent after persisting it in one instance. 

##### Replication Architectures
---

* Single-leader architecture
* Multiple-leader architecture
* No-leader architecture


##### Sharding
---

This is horizontal partioning of database. Shards can be divided based on some column value, or hash function or consistent hashing. Sharding is considered when:

- Data size is huge
- Need performance for read/write
- Reduce Latency (example: by sharding based on geolocation)
- Cost increases with sharding



##### Database Types
---

* SQL : Schema needed to start

* NoSQL : Not required to follow the exact schema

	- Key-based : Example- DynamoDB, Redis
	- Column Oriented : Casandra, HBase
	- Document Based : MongoDB, CouchDB
	- Graph Based : Neo4j (Nodes can be entities and edges can be relationshipsu)


## Architectural Patterns

* Monolithic : One componet has all the business logic
* Layered: Components are grouped into layers. Each request will go through the layers perfoming some business logic
* SOA - Serbice Oriented Architecture : Components are reusable via service interface which performs specific functionality
* Microservices: Components are collection of small independend services
	- Highly maintainable
	- Losely coupled
	- Scalable
	- Independently deployable
	- Single responsibility principle

## Message Queue

## Communication Model

* Push : Sender sends msg to receiver whenver msg is available
* Pull : Rxv asks sender if msg present

#### Communication Protocols
---

* HTTP(S) : Request sent and response expected. Server if doens't have response may not give any response and then the client has to make another request to get the data after sometime. 

* Long Polling : Request sent and keep waiting once the response is prepred by server. So no immediate response is expected. After receiving the response anothe Long polling request is sent to get anothe response. 

* Websockets : Duplex channel is created untill client or server closes the channel explicitly. The data is exchanged via this channel both ways. Example: Telephone call


## Security

* Authentication
* Authorization
* Encryption
* Data Integrity
* Symmetric Encryption
* Asymmetric Encryption
* Certificate
* Certification Authority (CA)
* Cross-Site Scripting(XSS)
* Denial of service (DoS) : One client sending requests
* Distributed Denial of Service (DDoS) : Multiple clients sending multiple requests





