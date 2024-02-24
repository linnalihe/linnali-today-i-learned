---
date: 2024-02-16 23:55
tags:
  - software-development
internal-links: []
---
![[Pasted image 20240216235616.png]]

We want to be able to build scalable systems
as the users increase the response time should not increase
![[Pasted image 20240216235944.png]]
Design a URL shortener

Depth vs Breadth Analysis
Define the functional requirements
- given a long url, shorten it and return the short url
- if someone hits the long url, direct the user to the long url
- if the long url is entered multiple times, need to return different and unique short url each time

![[Pasted image 20240217000905.png]]

Not focused on scalability yet or traffic
Focus on the core functionality that needs to be implemented
Ask clarifying requirements in the first 3-5 minutes to come up with the functional requirements. These are nailed down for the rest of the interview, do no deviate or add more to the functionality.
Interviewer is looking for you to be able to communicate and translate a open-ended question into clear requirements

1. come up with functional requirements
2. bucketize the functional requirements into microservices. Try to cluster but if they API and data models are not the same, put them in different buckets. Either a depth oriented where you have a monolith or a breath oriented which is microservices
3. draw the logical architecture so that each block is a microservice. draw and explain the data/logic flow
4. deep dive into each microservice or if there are many pick ONE in particular. talk with the interviewer to pick which one to focus. still assuming scale is not in consideration yet

##### 4 URL Shortener Naive counter
##### 5 URL shortner sophisticated counter

`decode(short_url) => long_url` is a search

`encode(long_url) => short_url` is an insert

Hash table to obtain O(1) time
How to create a short URL based on a long URL? 
1. steadily growing global counter => not so short after a we get to millions or billions, predictable and public will know how many urls have been generated
2. use base 16 which uses digits 0-9, A-F or base 62 which uses 0-9, A-Z, a-z

Take any number and convert it into any base

Base-2 machine 0-9
If we use the number 23
Think about having 23 balls in the leftmost cell, group them into 2 balls and keep grouping until there is only 1 ball

![[Pasted image 20240217231454.png]]
![[Pasted image 20240217231619.png]]

Might be asked to code the encode function
```
currval = n
while currval >= x:
	quotient = currval / x
	remainder = currval % x
	// map remainder to a digit in base x, and append it to the growing
	// list of digits
	currval = quotient
// map currval to a digit in a base x
reverse the list of digits and join them into a string
return 
```

Integer division by a power of 2 is like dividing a base 10 number by a power of 10
When dividing by power of 2, result quickly obtained by clipping out from left, right shifting by 1 place. dividing by 4 is shifting by 2 place, 64 shift by 6 places whereas 62 might need some more complicated

Mapping remainder to characters in base 64 by first determining the order of the characters. 
digits = `0123...9abcde...zA...Z-_`
look up based on index

![[Pasted image 20240217234425.png]]
![[Pasted image 20240217234835.png]]

This solution will sill have no colitions, url actually short but it's still predictable

##### 6 URL shortener cryptographic hash function
Can try to generate them in an unpredictable way
Having a short url of 6 or 7 is good enough => 64^6 or 64^7
random generation have increase the possibility of collision

But we can remember that with a hash table, we have a hash function that maps the key to an index integer for an array under the hood. A good hash function will distribute and decrease the collision. Implements linkedlist to resolve collisions as long as linked list is a constant length

Cryptogrpahic hash function was developed to ensure message integrity by taking a long message to a fixed length hash that is 128-bits or the mesage digest
The reciever will receive the long message and the hash function
Repurpose hash functions like MD5 or SHA-1 where input is long url and output is short url. We know collision is very low.

Add a counter or timestamp to decrease the likelihood of collision and then shorten the hash length which will increase collision but then you can try again to generate

a short url generated does not have to depend on what the long URL is. Can therefore pre-generate short URLs and store them in advance. can sort and remove duplicates.

##### 8 network protocols
Once we decide on the implementation, we need to consider access
Use going to a bank to illustrate how users interact with the service.
Will need to know the address of the bank or the URL of the webservice
The DNS is a table that maps the human readable url and returns the server ip address

Once you go to the address of the bank, it might be in a building that has other shops, you need the suite or specific room. Likewise, the request will also need to know what service the request is intended for because the server machine can be running other services. Will need to provide the port number to identify specific service. Also need to know what client process made the request.

Both request will include the IP address + port number, also IP address + port number of the client
![[Pasted image 20240218003231.png]]

PROTOCOL
- formal description of message format
- rules to be followed during the exchange
- HTTP protocol
- curl can be used to send an HTTP request
- port 80 is the default port for HTTP

Request is a package of information and the response is also a package of information.
Server code can't assume a short url will come in directly, need to parse http request and return it as part of http response

##### 9 Web servers
![[Pasted image 20240218092903.png]]

Parallelism - use multiple copies of the running program on multiple processors

Avoid blocking I/O
- performing i/o takes a long time
- while one copy is blocked waiting for i/o, the processor can serve another request

But all copies of our running program should have access to a single shared hash table and a single shared counter. <mark style="background: #FFF3A3A6;">Threads</mark> are copies of the program that access the same data. Processes are ones that have independently have their own data

Shared data can be in memory or in disk

As application developer, need to write the "critical section" of code where threads are sharing the same data will have acquire a lock and release a lock. But we can use a DBMS (Database Management Service) which will handle this. We will outsource to a DBMS. 

![[Pasted image 20240218093908.png]]

Application server should be STATELESS, all the information is in the database server. Similarly, if you go to a bank, any bank teller can pull up your info from their database and serve you.
Applications that store data locally are STATEFUL

As long as applications are STATELESS, we can scale up the servers that serve the application to handle the increase in traffic.
Add a load balancer in front of the application servers

Similarly to people waiting in the waiting room, request queue are lining up the requests and response queue to send out responses that are piling up

Flask, Django are web server frameworks

![[Pasted image 20240218095228.png]]

3 tiered architecture when there is a website that the user interact with.
2 tiered when it's a CLI and there is no display
There is likely a cache tier that the app usually checks first before looking at the DB

![[Pasted image 20240218095514.png]]

##### 10 Hash Index
How do we store the hash table on disk?
We can use whatever data structure in memory because we can access it easily
It's harder on disk, doing an arbitrary disk seek to find the location of the data is very expensive. We want to store data sequentially on disk.

Insert is O(1) because append at end of data file
For search, default is a O(n) by sequential scan. We can use a INDEX which is a hash table in main memory of database server that will allow us to do a single disk seek on the data stored on disk

##### 11 Horizontal and vertical scaling
- Gather non-functional requirements or capacity requirements and check whether and how to scale tier
- Propose a distributed system for each tier

Reasons to use a distributed system
- if data is too large to fit into one server
- if number of requests per second is too huge for one server
- time it take for application to process a response is too high so we want to parallelize the computation across different servers
- make sure that we are availability and reliable by having backups
- if users are spread across geolocation, to minimize the latency by using multiple servers in different locations
- hotspots in the data, high load on a piece of data

##### 12 URL shortener scaling for data size
non-functional / capacity requirements might be given by the interviewer or you might have to come up with it with a back of envelope calculation. research some numbers for many popular architectures #todo
 - 2 to 3 billion short links are created every year ~ 73 qps (query per seond)
 - 20 billion clicks per month ~7700 qps

reads to writes is 100 to 1. There are a lot more reads

Use a variable to do the calculation so that you can plug in afterwards, minimize the focus on a specific number in case it's wrong

3 years as a time for looking at data which is 365x3 ~1000 days
And number of seconds per day is 24x60x60 is 86,400 ~100,000
And number of seconds in 3 years is 1000x100,000 = 10^8

Therefore, number of k-v pairs is Qx10^8 where Q is the variable for qps

2048 characters text is ~2KB

If we are looking at a counter value that goes up to 10 billion
If we use base64, need to determine the length of string that can count up to 10 billion

Deriving this, we look at
1,000 == 10^3 == 2^10 is 10 bits
1 billion == (10^3)^3 == (2^10)^3 == 2^30 is 30 bits

2 billion == 31 bits
4 bilion == 32 bits
8 billion == 33 bits
16 billion == 34 bits

We take the 34 bits and group the into 6 characters at a time because 64 is 2^6. And 34/6, the ceiling of that is 6 so we need 6 characters for our short url which is 6 bits

multiply 10 billion by 2KB is
equals 10^10 x 2x10^3 bytes
equals 20 x 10^12 bytes
equals 20TB (1x10^12 is 1TB)

Using a variables
number of k-v pairs over 3 years is Qx10^8
size of the k-v pair as B bytes
total space required is Qx10^8 x B

1 to 2 TB of disk space per commodity machine so approximately 10 machines or 20 machines depending on size

partitioning and sharding the data across the 10 machines
each shard to be replicated 3 times so we need 30 machines

Caching rule of thumb is
10% entries in cache will result in 90% hit rate
20-30% entries in cache results in 98 to 99% hit rate

So if we store 20% of the data thats 20 TB x 20% is 4 TB
Cache is storing in memory
commodity machine will have 128GB ram
We need 4 TB and if each machine has 128 GB, we need about 30 partiitions or 40 partitions
So we need 30x3 machines in the cache layer

##### 13 URL shortner scaling for throughput
Do we need to scale for throughput?

We have look at and consider at any tier (app, cache, db) how many requests it can process per second
let x be the time in ms needed to process a given request, by a single thread. Then the number of requests would be 1000/x

Assuming 8-12 cores, 8 threads per core, 100 such treads per machine
That means (100x1000)/x requests/sec at 100% CPU utilization
That means (30/100)x100x(1000)/x = 30000/x requests/sec at 30% CPU utilization

![[Pasted image 20240218134355.png]]

At the app tier, there are multiple app servers, we need a load balancer
At the cache tier, there are multiple cache servers as shared and replicated, which need a load balancer and router to go to shard with requested values
At the db tier, there are multiple db servers as shards of replicated data, we need a load balancer and router to go to shard with the requested values

##### 14 Performance Metrics
How do we measure the performance of scalable system?
Instead of asymptotic complexity like we use for algorithms, we use SLIs or Service Level Indicators to quantify the leave of service being provided
1. Correctness is required. Logic of the application code. Error rate as a fraction of all requests
2. Availability: could we respond to the request? 
	1. fraction fo time that a service is usable
	2. fraction of well-formed requests that succeed
	3. 99% is 2 nines, 99.999% is 5 nines, 99.95% is 3 1/2 nines
3. System throughput is the number of requests per sec that could be handled
4. Response time is how long it took to return a respond to the client request. A good response is under 300ms, any longer and the user will likely leave
	- an experiment done when 400ms to 900ms, 25% of users dropped out
	- this might be caused by location and distance from server

Might be concerned with the uses in the percentile where the response time is longer because they are your valuable customers (might have more data stores, more complex requests, etc)

##### 15 SLOs and SLAs
SLI is Service Level Indidcators
- correctness
- availability throughput
- response time
SLO is Service Level Objectives
- a target value/range for an SLI (e.g. SLI <=target)
- p50 is 50th percentile
- e.g. p50 < 200ms and p99 <1 sec and availability >= 99.9%
- functional requirements describe SLOs for correctness, non-functional requirements describe SLOs for availability, throughput, responsetime
SLA is Service Level Agreement
- explicit/implicit contract with your users on what SLOs are , including the consequences fo meeting/missing the SLOs (e.g. rebate/penalty)
- Usually part of B2B service contracts

##### 16 Latency, Response time and Bandwidth
Latency is duration that a request is "latent" or awaiting service not being actually served
Response time is latency (2 way round trip time RTT) + service time at server
latency is measure of network performance or delay

bandwidth is the number of bits that can be transmitted over network per sec. best possible performance
throughput is the actual data rate, will be equal to or less than bandwidth
speed of light through vaccum is 3x10^8 per second
speed of light through fiber optic is 2x10^8

![[Pasted image 20240218145652.png]]

How large can a propagation delay be? 
Taking circumference of the earth ~40,000KM, 40,000x10^3 m
speed~ 2.10^8
The propagation time is distance / speed which will be (4x10^7) / (2x10^8) which is 0.2 sec or 200 ms. This is the best case

bandwidth is 10mbps
To transmit 25MB we convert it to bits
which is 25x10^6x8 where 10^6 is bytes and 8 bits per byte
and bandwidth is 10x10^6 bits per second
that will take 20secs
If the bandwidth is 100mbps then it would take only 2 secs

When transmission time dominates the response time, increasing the bandwidth will help. In other words, if the entirety of the data can fit in the pipe then increasing the pipe size or bandwidth is not going to help but if you have to chop it up and pass it though then increase bandwidth will decrease time

##### 17 Reverse and Forward Proxy
When client requests a site, the DNS likely returns the IP address of the load balancer
Everything behind the load balancer could be walled off from the internet in a private data center where address of machines inside are not available to the internet

A proxy is an intermediary between the client and a server, therefore a load balancer is a proxy. A reverse proxy is on the service side and forward client request to the server so that the client doesn't know which machine they are communicating with

Reverse proxy is a server side poxy that the servers hid behind
- load balancer
- decryption so that it provides the servers within the walls of the service with the decrypted key and encrypt it before passing it outside

Forward proxy is on the client side where the clients hide behind the proxy
- act as a web cache
- content filtering

##### 18 Load Balancing
the load balancer can handle a lot more requests faster than the application servers. That's why one load balancer can route to multiple application servers
Load balancers are often open source software that can be implemented like nginx
How does it decide how to choose which server?
- round robin
- least # connections or least response time
- random
- hashing

load balancers are for
- increase throughput
- increase availability so there isn't a single point of failure
load balancer is now a single point of failure so we need a passive backup load balancer that will have the same ip address and will transition to active if the previous active is down
Can also have 2 active load balancers and the DNS can be configured to send traffic to these 2 load balancers with different ip addresses

##### 19 IP Anycast
Can also configure so that the load balancing can associate client to closest server
Server the same IP address in different locations

Most ip address is unicast which means ip address is intended for 1 machine
multicast is message will be sent to all machines that has this ip address
anycast is that message is sent to any ONE machine with this ip address

Using graph data structure to find the closest machine that has the IP anycast address

for large companies the servers are owned by the company and the routers that the servers are attached to are also owned by the company
undirected graph and weight associated with the link
find the shortest path to any machine that has the same ip address as the machine that the client hits
each graph considers itself the source and runs djkstra's algorithm or something similar so that it knows the shortest path from itself to every other node in the graph

how the information is shared is that each node will broadcast who it's neighbors. Every router will have an edge list

##### 20 CRUD
Create, Read, Update, Delete
When using has index, in memory
If you delete key from in memory hash table it would become in-accessible
An update can be a deletion with a fresh insertion
a compaction process is run regularly to build up the new sequence and switch over

##### 21 Single Leader Data Replication
Have a single replica of the database servers handle all the writes
leader and follower
leader sends a log of it's changes to the followers and they apply same sequence of changes from changelog 

There is a wait time for all replica to update. You may see stale data until replication is completed. Eventual consistency
Strong consistency when client can't tell the system is replicated / they don't see any inconsistency. It's hard to have strong consistency in distributed system unless tradeoffs are made. Compromise on the throughput

##### 22 Multileader Data Replication
2 data centers, each with a write leader
What if there are 2 conflicting writes? 
Break tie by using timestamps to writes in UTC and take the last write

##### 23 Leaderless Replication with Quorum Reads and Writes
When writing, send write request to all replicas and if majority acknowledge write, it's successful. When reading ask everyone opinion and take the majority view

The reasoning is we want at least one replica to have acknowledge the write AND read
![[Pasted image 20240219120520.png]]

W+R > N for this to work
If N=3 then (W: 1, R:3) or (W:3, R:1). When W:1, write will be faster and read will be slower
Result in eventual consistency. While writing is happening, the read quorum will return stale value because there will be more replicas with old data

##### 24 CAP Theorem
There are two data centers 
We can have either leader or leaderless scheme
If the network between the two data centers gets disconnected causing a network partition. Availability will be affected because if a user is sending a write operation and it can't propagate to the other data center, we can't perform the operation

Consistent when Partition or CP meaning each data center will be unavailable for writes until connected again but consistently will be maintained 
Available when partitioned or AP meaning each data center will work independently and be available for writes
Can choose one, can't have both consistency and availability

CAP Theorem is when network is partitions , a distributed system can either be consistent or available (CP or AP), it cannot be both

##### 25 Content Distribution Networks
By having multiple data centers we can improve response time for reads and writes
we can also just focus on optimizing the read operations
Instead of setting up 3 tier in another data center, just set up an app cache
Proxy cache on the server side
There may be really popular content and access frequency will look like a long-tail
Benefits include
1. server load balancing
2. faster response time
3. congested network (higher fees to the ISPs)

CDN store data that the users access close to them for static content
![[Pasted image 20240219125208.png]]

If you are not a LARGE company you can use Akamai (pioneered this distribution scheme) and CloudFlare

##### 26 Cache Reads and Writes
Focus on the cache tier within the application once we're at the application level. 
The primary focus of the cache tier here is not to improve response time for clients
- not for response time improvement
- not for availability improvement
- It's for improving throughput!!!
- without cache tier, we will need a lot more database replicas
When creating or updating the DB, to keep the cache consistent, delete the key-value pair so that it will be updated when the application goes to the DB to retrieve and get back the most accurate data

Cache evictions can be done randomly, FIFO, or LRU. LRU has been studied and shown to be the most effective.

##### 27 LRU Cache
Use a hash table with a doubly linked list
#todo go back an take notes

###### 28 Cache Tier Strategies
Up until this point, the cache didn't directly interact with the database but it can
Cache-aside 
Can replicate cache tier and use leader/leaderless scheme
Assume 98-99% of data from the cache tier
Cache write happens when there is a cache miss or there is a create/update in the DB
As long as the there is enough data in the cache to make reads fast its not as important that they are consistent. The database is the source of truth.

Another scheme is the cache read-through and write-through where the application only interacts with the cache tier. The downside here is that the writes are slower than the cache aside because you are having cache acknowledge the write before returning to application

Yet Another scheme is the cache write-back or write behind where the cache doesn't immediately write to the database. It will write periodically to the database or right be for a cache eviction of  data. Risk here is that the data in cache is in memory and if machine crashes then we lose data

##### #### 29 Sharding
Up until now we were assuming that the data size was small enough to fit into one machine and there are more reads than writes

What if data was too big to fit into one machine?
What if write queries per second is too high for one machine to handle?

Then we need to partition/shard across multiple machines
total data size/space available in a commodity machine = number of machines
most commodity machines can assume to have 2TB of disk space and 128GB of memory

How do we map the key-value pair to the shards? We want to spread the data evening between the shards
We want the key-value pairs mapping to the shard to be unpredictable but deterministic so that a key-value pair lookup always goes to the same shard

![[Screenshot 2024-02-20 at 8.29.31 AM.png]]

Within the shared server, we would use a hash index to store and access the key-value pairs 
If it's a sharded cache server, it will use a hash table and a doubly linked list

Request routing for a CRUD operation along with a key-value pair or just a key for a read operation will be handled by a partition-aware load balancer or router
If shards are also replicated, the partition-aware router will choose from the replicas and act as a load balancer. Keep track of the health by sending health checks

###### 30 Resharding
what if we want to increase the number of shards over time? With the scheme we discussed above, we would need to rebalance or resharding. 
###### 31 Consistent Hashing
Instead of keeping track of the number of servers or mapping to the server, map to `box id` instead of `server id` and then have the servers assigned ONE `box id`, and everything before up until the `box id` that is assigned to another server goes into this server.
![[Pasted image 20240220090412.png]]
When the pink truck arrives, only 2 boxes has to be moved
`(b/N)` movements instead of having to move `b` boxes when extra truck or server is added.
Reduced work by a factor of N
Shard or `box id` is fixed and the servers can be dynamic
Replicas of the the shard server can be in the subsequent servers along this ring

![[Pasted image 20240220090512.png]]

A software service called zookeeper can be in charge of managing the servers and their health. Shares the box to server mapping with router and orchestrates movement of the boxes when a server fails

###### 32 Key-Value Stores
bigtable by google, 
dynamod by amzon, 
cassandra by facebook, 
azure cloud storage by microsoft
redis and memcache
mongodb is document store in json 

DB structure can be different and still use a key-value cache in memory

###### 33 Relational Database and Tree Index
Well known way of storing into database is key-value pair to structured n-tuples
This is what a relational database stores data as
Rigid and has constraints on the data being stored
![[Pasted image 20240220105937.png]]

Can use a hash table index or a tree index
tree index is good for finding a set of data within a range, and index on columns that has duplicates
###### 34 SQL, Normalization and Foreign keys
SQL statements tell the DB what the selection or filtering is; conditions to be satisfied
How the query is implemented is specific to the DB
We can use indexes to optimize some of the queries if we know we use particular columns more over others
each DB will have it's own SQL optimizer that will answer the query given
rule of thumb is to create separate tables to decouple or normalize by removing duplicate information, using foreign keys to associate to data that is normalized into another table
- When there is a one-to-many relationship. one person, many jobs
- When there is a many-to-many relationship. a product can be purchased by many customers, a customer can purchase many products

##### 35 ACID Transactions
Normalizing or removing redundancy costs time. Joins are costly
Transaction is sequence of DB operations that must be executed as a unit (all or none). example is making a payment to make a reservation

Atomicity meaning it's a single unit, either fully complete or not complete (logging an recovery)
Consistent or run correctly. Takes from one valid state to another valid state
Isolation means if there are more transactions, other transactions will not affect each other. Idempotent (concurrency control manager)
Durable or permanent recorded. If there is a crash, it will be saved and retrievable (logging and recovery)
##### 36 Big Data and NoSQL
Relational databases dominated 15 years ago

Nowadays we have usecases in web logs, social media, mobile apps, sensors(IoT) caused need for other database solutions
Rate at which the new data is generated also exploded
Variety of new data is now semi-structured and diverse
This new data is called Big Data
May not need ACID so we look at document stores, etc. Might choose to duplicate data and just have things stored in JSON

There is now a mix of functionality that has both relational and noSQL solutions
Google Cloud Spanner, Cockroach DB, etc