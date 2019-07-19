# Content

- Concepts
- Architecture, Design and Implementation aspect(s)
- 12-factor apps
- Cloud native apps
- Benefits
- Best practices
- Good to know

Please look for tag [Architectural and Design aspect] if you are interested in architectural aspects specifically.



# Concepts

1) What is microservice?
2) What is microservice not?
3) Monoliths vs. Micro-services
4) Microservice precursors


### What is microservice?

A fine grained atomically deployable service accessed by platform agnostic network API.

### What is microservice not?

It is a pattern! You apply where it fits. It is not supposed to be applied everywhere. :-) There are many other architectural 
patterns which is not going to go away. For e.g. monolithic systems for equity transaction as the focus here is of getting 
low latency than anything else.

### Monoliths vs. Micro-services

Django kind of application relying on MVC pattern assumed layered architecture. It did apply separation of concern
by segregating user layer, business layer and persistence layer. But all business layer was embedded in one place.
So, if there is any scale required in some portion of business layer, entire model needs to be changed. That's why
it is called a monoliths application. 

Micro-services are different. They divide a domain into of set of functional unites which can interact with each 
other. A good architect needs to understand this and should be able to create a design which address the following 
aspects:

    * x, y, and z scale
    * services are neither fat nor thin
    * ability to function a service without depending on each other 
    * divided around domain - a very fundamental need for designer to understand it

Be careful when we talk of scatter gather or MVC vs. micro-services patterns.


### Microservice precursors

The chronological order are:

	Service Oriented Architecture (SOA)
		+ Service 
		- Layered architecture 
		- Usage of ESB (anti-pattern of Smart endpoint and Dumb queue)
	Domain-Driven Design
	Hexagonal Architecture
	Agile methodology
	Continuous integration and Continuous Delivery
	Virtualization
	DevOps (a new paradigm of release software)




# Architecture, Design and Implementation aspect(s)

See https://github.com/jranjan/labs-technology-bytes/edit/master/micro-services/microservice-patterns.txt 


   

# 12-factor apps


It is very important to understand 'difference between microservice and webapp' before we proceed.

OpenStack was competitor of AWS. Same with Heroku and Cloud Foundary. Web app is designed to face world wide web but not microservice. 
Web app W1 wants to talks to web app W2. In this case, W1 has to travel north-south traffic and go to wwb and come back  to W2 even 
if they are deployed in same environment.

12-factor app is meant for web app but everything is not meant for micro-service. By webapp, we mean by Heroku app here. Do not read 
the difference as hard difference between but as a subtle variation or flexibility in case of microservice (like CaaS) as every
container is not a web-app.

1) Codebase
	- Rule: Each deployable app is tracked as one codebase in revision control.
	- Same for microservice and webapp
	- Push code for Heroku and application gets deployed.
	- Push code in Kuberentes and set of actions takes place to deploy new container.	
	
2) Dependencies
	- Rule: An app explicitly declares and isolates dependencies via appropriate tooling (e.g. Maven, Bundler, NPM) rather than 
	  depending on implicit dependencies in its deployment environment.
	- Difference between microservice and webapp
	- In case of PaaS, as you push the code, it builds the code. While in case of CaaS, you push container images. In other words,
	  container images is the unit and use same for integration testing, governance issuse, license checks before deploying it but 
	  PaaS does not emphasize so much of immutable images. Build once is the mantra of container but not of PaaS.
	  
3) Config
        - Rule: Configuration or anything is likely to differ between deployment environment is injected bia operating system 
	  level environment variables.
	- Difference between microservice and webapp
	- Microservice rely principally on dynamic discovery mechnaism (DNS etc.) for environmental variations. On the other hand,
	  PaaS environment is little bit strong headed in usage of environment variable. So, usage of configmap, secrets etc are 
	  common pattern in CaaS but not in the case of PaaS
	  
4) Backing services
	- Rule: Backing services such as databases or message brokeres are treated as attached resources and consumed 
	  identically across all environment
	- Same for microservice and webapp
	
5) Build, release and run	
	- Rule: The stages of building an artefact, combining that artefact with configuration and starting one ore 
	  more processes from that artefact/configuration combination are strictly separated.
	- Different for microservice and webapp

6) Processes
	- Rule: The app executues as one or more statelss process.
	- Same for microservice and webapp

7) Port bindings
	- Rule: The app is self-contained and exports any/all services via port binding (including HTTP)
	- Differenct for microservice and webapp
	- Containerized microservices can listen on any port they desire and benefit from consistent port usage across replicas

8) Concurrency
	- Rule: Concurrency is usually accoomplished by scaling out app processes horizontally (though processes may also multiplex
	  work via internally maanged thread if desired)
	- Same for microservice and webapp

9) Disposability
	- Rule: Robustness is maximized via process that start up quickly and shut down gracefully, there aspects allow for rapid 
	  elastic scaling, deployment of changes and recovery from crashes.
	- Same for microservice and webapp

10) Dev/prod parity
	- Rule: Continuous delivery and deployment are enabled by keeping development, staging and production environments as 
	  similar as possible.
	- Same for microservice and webapp

11) Logs
	- Rule: Rather than managing logfiles, treat logs as event streams, allowing the execution environment to collect, aggregate,
	  index and analyze the events via centeralized services.
	- Same for microservice and webapp

12) Admin processes
	- Rule: Administrative or management tasks such as database migrations are executed as one-offf processes in environments 
	  identical to the app's log-running processes.
	- Same for microservice and webapp



# About cloud native apps   

These are characestristics of cloud native apps.

* Operability
     * Expose control of application/system lifecycle
* Observability
     * Provide meaningful signals for observing state, health, and performance
* Elasticity
     * Grow and shrink to fit in available resources and to meet fluctuating demon
* Resilience
     * Fast automatic recovery from failures
* Agility
     * Fast deployment, iteration, and reconfiguration.
	
Everyone is part of CNCF even Microsoft, Apple, Amazon. All these because of Kuberentes. Rules of 12-factor apps goes well with 
cloud native apps but not all rules apply as it is.




# Benefits


	
* Architectural lities (scalability, availability, extensibility etc)
	- Fine grained scaling
	- Possible scalability for specific microservice 
	- Technological independence among microservices
	- Flexibility to embrace newer techonologies
	- Resilience
* Manageability	
	- Simplicity to identify errors
	- Independent deployment
* Engineering benefits from implementation perspective
	- Independence between teams
	- Implementation of isolation
	- An exclusive business domain for each microservice, facilitating the implementation of new features
	- Better definition of business without cyclic dependency between them
	- Organizational alignment
* Release
	- Deliever software faster
	- Response faster to change 



# Best practices: steps or habit to adapt for goodness

1) Identifying areas to apply the principle of sole responsibility for each micro-service is crucial to application architecture.

2) Express resilient published interfaces (not the public interface):

      * Published versioned interfaces: An efficient version control to indicate when something, deprecated is key. 
	Not only that, but it will also indicate what the new version is and when the deprecated version will be 
	deactivated permanently.
      * Small published interfaces: A large payload is much more susceptible to change than a more specialized payload. 
        Applying the concepts of DDD on these payloads is very healthy.
      * Published external interfaces: Do not create the concept of published interfaces for internal development teams. 
	This creates a slow process of change and implementation features.

    The published interface is what the micro-service developers release. The published interface is what will be consumed by the 
    internet. A good example is the Single Sign-On (SSO) API.
	
3) Deploy micro-services independently

4) Define upgrade logic taking care of the following points:

	* Never share libraries between micro-services
	* Strong delimitation of micro-service domains
	* Establish a client-server relationship between micro-services
	* Deploy in separate containers

5) Design for scale (beauty my friend):: x axis: horizontal scaling, y axis: functional scaling, z axis: data partitioning

6) Documentation of communication.
   The Swagger API is a good alternative solution for such problems.

7) Use endpoint builder strategy.
   In this case, the heavy point of information actually is compositions of other lighter data sources.
   Types: heavy endpoint vs. light endpoint. The former is big no for the mobile world.
   Whom it is meant for? web applications or mobile application.





# Microservice challenges 

Here are list:

* Explosion in the number of processes to manage
* New and not well understood by many teams
* Heavy netwrok utilization and increased latency
* Small to medium applications may be hard to deploy and manage
* Integration is not longer any one in development problem

Every challenge is area of opportunity :-), are you ready to solve it? 

# Open questions


#### What is reverse HTTP proxy? 

It is really confusing. In case of proxy, server does not know who it is talking 
to (client : server -> n : 1). In case of reverse proxy, client does not know who is talking to (client : server -> 1 : n).

Watch overview [here](https://www.youtube.com/watch?v=ozhe__GdWC8).


####  What is fail-over in case of cloud native application?

It is immuitability. We do not care who fails! Let us assume that we have 


#### What is role of Zookeeper? 

Zookeepr helps in figuring out leader in quorum. It implements PAXOS algorithm - a difficulty and one of the most researched
items among academecian.


#### What is role of gRPC and ProtoBuf in gRPC/ProtoBuf?


#### What is service mesh?






# Glossary


### Bounded Context:

Bounded Context is a central pattern in Domain-Driven Design [DDD] (term coined by Eric Evans in his 2003 book). DDD decomposes
complex software systems into bounded contexts based on natural partitions in the real world problem domain and the unique
perspectives that those contexts embody. 

See https://martinfowler.com/bliki/BoundedContext.html for more details.

