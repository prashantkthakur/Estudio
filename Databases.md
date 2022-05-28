# Databases

* Polyglot persistence - Use of multiple storage technology for the application.


## NoSQL

#### DynamoDB

Key-value database, which means its a columnar database. (MongoDB Document store)
Data storage is on SSD 
Allows secondary indexes; uses REST API to access the data 
No server side script (MongoDB YES javascript)
Integrity model support: BASE (Basically available, soft state, eventual consistency);
 MVCC(Multi-version Concurrency Control); ACID; (MongoDB BASE only)
 Eventual consistency; log replication; Read committed
Atomicity: Yes (MongoDB Conditional)
NO transactions full text search or Geospatial indexes (MongoDB YES)
Max size value : 64K (MongoDB 16MB)
ORM : no
Operations performed per sec: 1K (mongoDb 10K)
Function based index : Yes (MongoDB NO)

Choose if you need:

* scalability, low latency
* auto-scaling 
* auto-sharding
* very high read-write rate even though data size increases
* require tera-bytes of storage capability
* key-value or simple queries are present
* high durability
* no tuning
* no downtime
* requirements are dynamic and the changes are based on traffic and usage scenarios with desired throughput


Don't Choose

* multi-item or cross table transactions are required
* when complex queries and joins are required
* real-time analytics on historic data is required

#### DynamoDB Vs Casandra

 DynamoDB only has String, and Number as attribute types. 
 Casandra has various - Integer, BigInteger, ASCII, UTF8, Double etc and offers composite and dynamic composite columns.
 
 Casandra provide the full range of data formats, which includes structured, semi-structured and unstructured data.
 
 Cross region data replication : Supported by Casandra not in DynamoDB
 Multi-AZ :  for DyDB
 
 
 DynamoDB has atomic counter that is more reliable with low latency. (If need of large number of increments on few counter) 
 
 Casandra - scaling up is easy but scale down is slow, manual, error prone.
 
 Scaling - DynamoDB scaling is automatically handled and easier than casandra and has less work, takes less time for the process.
 
 Disk - DyDB is managed automatically. 
 For specific key - DyDB has limit up to 64KB while Casandra depend on the node disk size.
 
 Casandra - has ACID compliance using commit log, with a built-in redundancy that ensures data durability if the hardware fails.
 
              DynamoDB; Casandra
Data model : Key-Value; Key-value with wide column store
Data store : SSD; FileSystem
Secondary Index: Yes; No
Accessing Method: API call; API call, CQL, Apache Thrift
MapReduce: No(can be done with other aws service); Yes
Integrity model support : BASE, MVCC, ACID, Eventual consistency, log replication, read committed; BASE


 #### MongoDB
 
* Document oriented database.
* Uses internal memory to store the (windowed) working set, enabling faster access to data. But what if data is more than accessible memory. DyDB scales better in this sense.

* 