# Contents

- Story of microservice's communication
- Devising loosely coupled systems
- Cloud native transaction and event processing
- Orchestration of microservices
- Persistence in polyglot environment
- Microservice pattern


![Ask yourself](/micro-services/images/recall.png)
### Did you ask requirement of cloud native application?
   1. Needs durabiltiy! Do not rely on disks for durability! Rely on clustering.
   2. Immutability! Fail over is not so fancy strategy for durability. In stead, it is better to use set of immutable instances.
   3. Need resilency! Mechanism to resilency is typically replicas. Note that replication is very different from scale.
   4. Easy scalability! Many ways to achieve. Auto-addition of instances or partitioning are some ways to achieve same.
   5. ATOMICITY. Application must be designed to take care of this. Leverage some tools or sequencing or something else to achieve it.



# Story of microservice's communication


For asynchronous communication, use the pattern of 'Smart Endpoint and Dumb Pipes". Use message broker, e.g. RabbitMQ, 
ActiveMQ, ZeroMQ, Kafka, and Redis. Each of these options has its own peculiarities, some are faster, others are more resilient. 
Again, the business setting is going to determine which technology is used.

Communications are the lifeblood of microservices. The following schemes are typically employed:

1. Request/Response
    * REST
    * RPC
	* gRPC/ProtoBuf          
	    * Nice properties while decomposing monolith application
	    * Complier makes it platform agnostic unlike REST where there are different SDK like bottle, flask etc.
	    * Claim to be protocol independent but uses HTTP 2.0 mostly for implementaiton.
	    * Apache Thrift
	    * Fast
        * JSON RPC			
2. Messaging
     * Broadcast
     * Pub/Sub (aka multicast)
	 * Kafka
	 * NATS
	     * Anycast
	     * Unicast
	     * AWS SQS and SNS.  It is good if services are running in AWS
3. Streaming: characterised by a continuous flow of data from a server to a connected client.



### REST (request and response)

1. HTTP 1.0 vs. HTTP 2.0, a huge difference in terms of efficiency

2. Six Constraints. These are not limitations but a way to implement RESTful API which adhere (or live under) these guidelines. 
   It is API style not a protocol - always remember!
	* Client-server
	* Statelss
	* Cacheable
	* Layered system
	* Code on demand (optional)
	* Uniform interface
	
3. RESTful web services
        * A web application implemented using HTTP and REST principles.
	* Collect of resources
	* Idempotent methods: GET, OPTIONS, HEAD, PUT and DELETE. 
	* PATCH vs. PUT. PATCH is not idempotent but not PUT. For e.g. if your PATCH call is applied 10 times, it should 
	  do same thing 10 times. For e.g. adding a phone number.
	* REST is not protocol but uses standards like HTTP, URI, JSON etc.

                         | HTTP method    | Idempotent   | Safe (immutable)   |
                         | :------------- | :----------: | -----------------: |
                         | OPTIONS        |    Y         |     Y              |
                         | GET            |    Y         |     Y              |
                         | HEAD           |    Y         |     Y              |
                         | PUT            |    Y         |     N              |
                         | POST           |    N         |     N              |
                         | DELETE         |    N         |     N              |
                         | PATCH          |    N         |     N              |

	
    Idempotent does mean that you get same result irrespective of number of times you perform the operations. 
    Think verbs at resource level! Does it create new resource? If no then it is idempotent.  For e.g. 
    once order id is created then it does not matter how many times you apply PUT with same payload. The answer
    is yes. 
	
	
4. Implementation platforms (YOU NEED TO GET HANDS ON!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!. Remember difference between: Specification
   vs. Protocol vs. SDK or platforms.
   
    			 | Language       | Examples                          |
                         | :------------- | --------------------------------: |
                         | Java           |  JAX-RS, Jersey                   |
                         | python         |  DJANGO REST framework, Falsk     |
                         | Scala          |  Play                             |
                         | node.js        |  Connect, Express                 |
                         | Golang         |  Ney/HTTP, gin                    |
                         | Ruby           | Sinatra, Rails-API                |
                         | PHP            | F3                                |
					
5. Open API Inititatives
	- Vendor neutral open governance structure under the Linux foundation
	- Swagger is adopted by OAI
	- RAML: a REST API codification competior to OAI
	
6. Backend API implementation
      * REST is predominantly used over the infrastructure of the web. 
      * In backend systems, web technologies can be cumbersome. This is becoming less the case with HTTP2.
      * Usage (not for frontend)
           * Apache Thrift: Facebook, Twitter
           * Protocol Buffers: Google
      * There are benefits to a homogeneous solution but non functional requirements must also be considered when
	designing end to end corporate solutions.
	
	

# Devising loosely coupled systems


3 popular techniques: Streaming, Messaging and RPC. It is worth to note the following points:
      
   * Streaming can be sync but is usually async.
   * Message is almost always sync or async
   * RPC can be either sync of async.
   * REST is sync by definition whe over HTTP. Schemes like chunked responses and long polling attempt to alleviate.

![Ask yourself](/micro-services/images/recall.png)
### Did you ask before picking message broker?
   1.  Do you need streaming kind of scnearios to be solved?
   2.  Your calls are aync or async?
   3.  Is odering of async calls are needed?
   4.  How much de-coupling you need, are you OK to have middleware?


### Usage of messaging system

Important aspects:

* Types of messaging systems
* High profile messaging platforms
* High profile messaging platforms


#### Types of messaging systems
     
  1. Raw Network messaging
          - UDP
	   - Fast as it gets
	   - True multicase, True broadcast
	   - No store, no forward
  2. Library based
           - Can offer some delivery assurance (retry)
	   - Coupled with publisher and cosumer. It does matter who comes first!
	   - Deploys in process
	   - No server or middleware required
	   - Super low latency
           - Examples: ZeroMQ, Nanomsg
  3. Message Oriented Middleware
           - Store and forward
	   - Completely decoupled with publisher and consumer. So, it does not matter who comes first! 
	   - Pub/sub
	   - Perhaps various transaction levels
	   - Routing 
	   - More complicate deployment
	   - Not for the fastest messaging solution
	   - Examples: NATS, Kafka, RabbitMQ, AWS SQS and SNS, AMQP
	      
#### High profile messaging platforms designed in pre-era of cloud computing
    
  1. Websphere MQ (IBM centric, trying to open source)
  2. Apache QPID (AMQP, free)
  3. ActiveMQ  (AMQP, free, apache community) 
  4. RabbitMQ (Erlang basedm elegant, fast, reliable, Erlang is used in Telcos)
  5. ZeroMQ (library based, very fast but brokerless, requiring rendezvous)
    

#### High profile messaging platforms
	
   1. Tibco Rendezvous [RV] (Popular in financial applications, fast and expensive)
   2. Information Ultra Messaging
    - ZeroMQ
    - NATS 
    	- Open soruce messaging brroker designed for microservices and cloud native applications
	- Faster in line with Kafka
	- Earlier written as Ruby, no in Go
	- CNCF project
	- Does not worry about persist the messages. Durability is lost!
   3. Apache Kafka 
    	* Created at Linkedin
	* 3 times faster than RabbitMQ
	* Scalable
    	* Persists the message for 7 days, gives durability!
	* Developed in Java
	* Collection of brokers! 
	* It seems to be more interesting and valuable. So, a detailed investigation is warranted!
   4. Apache Pulsar (Created at Yahoo)
   5. Redis
    	* Using it as key though it is in-memory key-value pair
    
    
    
# Ask yourself before choosing message borker?

   1. Do you neeed durability?
   2. What is your performance requirement?
   3. Do you neeed middleware? Or, are you fine with library mode for better latency like ZeroMQ?

  
    
    
    

# Usage of persistence in polyglot environment

Usageg of messaging was more towards loosely couple and achieving collaboration. A problem is still to be solved - that is about 
the mangaging state inside micro-service - which has been solved for years by RDBMS. Here, what RBMS gave:
	
1. ATOMIC transaction
2. Schema (semantic validation)
3. SQL (query) to get different set of data

Here is what RDBMS does not give or list of things which no-SQL consumers wants:

* No SQL
* Scalability: only scale vertically, no horizontal sale
* Concurrency: a notoroious thing to achieve
* Designed in era of single node computer or set of small cluster
	
In recent times, two patterns have emerged for scaling RDBMS or SQL database) but its own limitations of 
implementation complexity, usage of commodity hardware has resulted in failure:

* Most of databases use clustering  and replication mechnism. Some do auto-sharding (but primitive). 
* Applications natively build sharding algorithms and runs bunch of nodes. YouTube was using MySQL but now moved 
  to creating new one where they put a facade in front of MySQL which runs on set of distributed cluster. Query 
  is very faster. 
	
Looks like SQL is in many places! Head of its reference in Hadoop as well. Because of the following issues the world is moving 
towards NoSQL



### Common databases

Everyone mentioned below does support cross-feature. For e.g. DynamoDB supports key-value pair as well


   | Type                          | Examples                                                         | 
   | :---------------------------- | :--------------------------------------------------------------: | 
   | Relational database source    | Postgress, MySQL (old user Youtube)                              | 
   | Key-value                     | Redis (AWS), etcd, Memcached (AWS, Riak, LevelDB                 |
   | Document                      | SynamDB (AWS), MongoDB, CouchDB, MarkLogic, OrientDB, ArangoDB   |
   | Column                        | Neptune (AWS). Neo4J, OrientDB, ArangoDB, Titan, FlockDB, Giraph |
   | Graph                         | ?                                                                |
	
	

### NoSQL database

No schema and hence so semantic validation is required. But there is is hidden truth: there is implicit schemas. Few points:
      
   * Changing the structure of data during the life of an application has an impact on the application. So adding a filed is not
     a big issue unlike RDBMS where adding a column is a challenge.
   * Many NoSQL datanase operate on aggregates.
   * It works best as application databases - dedicated for single application - context oriented technology. 

### Mechanism to scale

1. Replication
     * Copies of same data on multiple nodes
     * More used for availability but gives scale as side benefit.
     * Benefits:
         * Read scale
         * Availabiltiy
         * Geographical distributability
         * Task Offloading: replcias present excellent platforms for backkup, analytis, application development and testing environment
2. Partitioning
      * Two ways: Vertical (sets of colums are placed on different nodes) aad horizontal (aka shading - sets of 
	rows are placed on different nodes). In fact, there is a third way - a combination of these two.
      * Vertical paritioning is little not worthy as you need entire row in entity
      * Horizontal partitioning (or sharding) is faster almost proportional to number of rows.

![Ask yourself](/micro-services/images/recall.png)
### Did you ask before picking persistent solution?

1. Mechaism to scale: replicaiton and partitioning (vertical or horizontal or combination of both) 
2. Do you query data most of times which involves join of tables? In that case, which partitioning technique.
3. Choosing a database does not only performance but for easy ccoding, retrieval, speed etc. So, look at application logic!
4. Choose application centric database!
5. Two most important thing to consider microservice design are: programming language and persistent mechanism (database)



# Cloud native transaction and event processing


Definition of state: All the stored information, at a given instant in time, to which program has.

### Elaboration of ACID theorem

1. Atomicity: requires that each transation is all or nothing.
2. Consistency: ensures that any transaction will bring database from one valid state to another.
3. Isolation: ensures that the concurrent execution of transaction results in a system state that would be obtained if 
   transaction were executed serially
4. Durability: means that once a transaction has been committed, it will remain so, even in the event of pwoer loss, crashes or errors.

### Elaboration of CAP theorem:

1.  Consistency: all nodes see the same data at the same time.
2.  Availabilty: gurantees every request receives a response whether successful or failed. 
3.  Partition tolerance: the system operates despite message loss or failure of part of the system. It is key to scale.
    
You can get two of above in any clustered system. For e.g. Swift claims for AP. 




# Orchestration of microservices

Container maangement is another form of orchestration

### Need

   1. Container scheduling
   2. Container management
       *  Monitoring and recovery
       * Image upgrade rollout
       * Scaling
       * Logging
   3. Service endpoints
	* Discovery
	* HA
	* Load balancers
	* Auto scaling
	* External services
	 	- Network configuration and management
		- Durable volume management

### Common orchestrator


   | Type           | Examples                                | 
   | :------------- | :-------------------------------------: | 
   | Open source    | Kubernetes, Mesos, Docker Swarm         | 
   | Cloud hosted   | AWS, GKE, EKS                           |
	
	
	
	

# Caching techniques.


Varnish Cache, defined as: the Varnish Cache accelerator is a web application also known as reverse HTTP proxy caching. 





# Commonly used microservice pattern


1. CQRS (Command Query Responsibility Segregation)
2. Event sourcing
3. The Saga Pattern
4. Smart Endpoint and Dumb Pipes

### Event Sourcing::

   *  It is the process of capturing all inputs as events
   *  Rely on immutablity
    
### The Saga Pattern
	
   * It is used to solve problem a business transaction span multiple service.
   * A saga is a sequenec of local transactions.
   * Implementation mechanism or coordinate sagas
       *  Choregoraphy
       *  Try to map it with General problem (of army one)
       *  Compensating transaction helps here.
	   
   
   

# Questions


   
### Can we pass body in GET?

See [here](https://stackoverflow.com/questions/978061/http-get-with-request-body)


### Can a distributed system be consistent any way?

Answer is no. It does not matter who tells what. Ceph tells that it is consistent storage system but in reality, it might not 
be. Swift (anyhow) declares that it is eventual consitent. A bitter truth is that all distributed systems are eventual consistent.
I am findinig difficulty to ingest. But the example of General(2)-Enemy(3)-General(3) problem gives data point which is difficult 
to ignore. So, time to revisit CAP theorem for distributed system? :-)

The problem is presented below:

![Attack enmey problem](/micro-services/uml/images/general-problem.png)

		   
You just increase the probability of consistency with multiple phase commit at the cost of high latency but you are never guranted. :-)	




# API Gateway

/3par
/3par/volumes
/3par/volumes/{id}
/3par/volumes/{id}/utilization
/3par/volumes/{id}/billing
/3par/volumes/{id}/events
/3par/volumes/{id}/accessed-datasets


Concerns:

Generic volume information
Utilization data: capacity and performance
Cost data
System events
Dataset patterns

Users:

General user
General user
Billing and costing user
Datacenter admin
Data analytics

Cross cutting concern:

Security (certificates)
Authentication
Authorization

API guards: acts as guard before hit to api url

1) Security
2) Authentication 
3) Authorization
4) Routing 
5) Adaptor, unifies client calls. Replacing multiple client calls to single calls.
6) Static files can be left shifted to API gateway. 
7) Cache
8) Load balancing: mobile calls and desktop calls are routed to different endpoints
9) A/B testing
10) Canary release
11) Protocol diversity

API gateway:

self managed
  Apache
  HAProxy
  nginx
  spring cloud gateway
  
public cloud

 Google cloud endpoints
 Azure API gateway
 AWS API gateway
 Apigee






