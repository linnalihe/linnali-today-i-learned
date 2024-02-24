---
draft: true
date: 2024-02-22 00:06
tags:
  - swe-job-search
  - system-design
internal-links:
---
3 categories of problems
1. online processing which consists of a collections of components that interact with each other and expose a piece of functionality. At the atomic level we have the CPU, RAM and Network
2. Batch processing
3. Stream processing

Online processing system sometimes have an offline subsystem, etc
![[Pasted image 20240222001730.png]]

How to approach mentally
- This should be a peer-to-peer discussion
- Interviewers should feel like they can work together with you
- About how you would build a system in the real world

Collect functional requirements first
Then ask for non functional requirements related to scaleability

availability is the percentage of time a request gets a valid response within a timeframe

Throughput is the request per second served

Using IAM (user authentication management) as an example:

Functional requirements
- determine who the user is
- determine what access they have

Step1. For each functional requirements

- map to API with the data model
- Questions to ask
	- how large is the data record?
	- how many data records?
	- how many API calls per second?
	- what should be the response time of the API?
	- how many write per second
	- non-function requirements to think about per API is availability, reliability, hotspot, geolocation, security

Availability is usually very important and usually chosen over consistency

Step 2. Identify building blocks
From here start treating the interviewer as a peer
Bucketize functional requirements into micro-services. If the data model and API look different they should be different micro-services. Should different teams handle them?

Depth or breadth-oriented problem depends on the number of microservices. More than two or three is breadth-oriented problem
Depth means you want to focus on a single problem and focus on the implementation of API, scalability, and bottlenecks. Maybe a pseudo-code style
Breadth you give an overview of the system and pick one to deep dive? or ask the interviewer what they prefer to focus on and pick one if they don't know

![[Pasted image 20240222091932.png]]
2nd category
- is something like map-reduce and aggregation of large amounts of data across different platforms, users, etc.
- Searching
- Indexing
- PageRank
- Recommendation
- Image processing
- or data validation of a specific style
- We want to do pre-processing or work during a time before user requests

3rd category
- DataDog, Grafana

Focus on the microservice implementation and then talk about how they talk to each other. 
- APIs
- Message queues
- ETL

Step 3. Propose the architecture
Block diagrams of the microservice
And communication method between the services
API gateway between users and systems

Explain data/logic flow
- if high volume of data needs to be pushed real time between microservices, use pub-sub with message queue
- pub-sub is a microservice by itself
- if data needs to be pulled from server to client, use REST API
- if data needs to be processed offline, consider ETL


2nd part of step 3
![[Screenshot 2024-02-22 at 9.46.14 AM.png]]

Brands (but at big companies they build in house tools)
![[Pasted image 20240222094755.png]]

![[Pasted image 20240222095359.png]]

Choosing databases
Row-based when the schema is well defined and each row holds a transaction or a group of data that relate to each together
Column-based can work for unstructured data where columns relate to each other, or when you have to pull out data per column. more analytical queries. append-only and use timestamps to indicate which record is the most useful. Uses compression algorithms to squash columns that have a lot of repeating values

Up to @3:00:00

Criteria for scalability
Template for estimation
Tradeoffs for sharing and replication

Questions for managers are mostly breadth-oriented rather than designing the API. Have a good idea of how the micro-services are orchestrated and scaling for latency and throughput

![[Pasted image 20240222135752.png]]

A commodity server can handle ~30,000 queries per sec

Do the estimation and compare to determine if scaling is necessary
Usually we will need to scale because it will be higher than this

100Million users
Assume that each person authenticates once a twice a day
(100M x 2) / (24x60x60) = 2,300 qps but usually not distributed across the entire day

Maybe assume that the window is 2 hours
(100M x 2) / (2x60x60) = 28,000 qps


Extras
![[Pasted image 20240222135544.png]]

![[Pasted image 20240222135600.png]]
Thinking through back of envelop for throughput
![[Pasted image 20240223094012.png]]

Thinking through storage
![[Pasted image 20240223095539.png]]
API latency reduction by parallelizing with more servers
![[Pasted image 20240223100215.png]]

Generic numbers to keep in mind as a comparison
![[Pasted image 20240223100350.png]]

Grouped into 4 groups of functions
![[Pasted image 20240223100726.png]]

Tradeoff between consistency and availability when network is partitioned
Options might be
- AP (eventual consistency) where you can write and read whenever but it takes time for the data to be copied over to the follower machines
- CP (strict consistency) with leader-follower, have to write to the leader and read from the leader or wait for the data to copy over to the followers
- CP (strict consistency) with a quorum to write to the write quorum where data must be written to multiple machines and read quorum to wait for enough machines to respond and verify data is consistent. Shard throughput is slow because we send our request to all shards and wait for enough servers to respond back

Critical for the interview
- functional
- non-functional
- API design
- breadth orient problem needs to provide a high-level overview

Not critical
- estimation of the numbers
- don't go into estimation without asking the interviewer

When there is high throughput and long processing time, do pre-computation. Probably with some offline batch processing manner. Caching is a way of pre-computation also

Become familiar with the tool that the company will be using for the interview

Communication is critical, being able to own the session
Need to talk about tradeoffs! Distinguishes people who have real work experience