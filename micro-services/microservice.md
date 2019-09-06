# Content

1. [Introduction](#Introduction)
2. [What is microservice?](##paragraph1)
3. [Third Example](#third-example)
4. [Fourth Example](#fourth-examplehttpwwwfourthexamplecom)

1) Introduction
2) Architecture, Design and Implementation aspect(s)
3) 12-factor apps
4) Cloud native apps
5) Benefits
6) Best practices
7) Good to know 

 
# Introduction

This sections covers many aspects, primarily to setting up the context with the aim to use same vocabulary. Practically, 
it deserves an independent chapter which tells how the landscape of delivering software has changed because of emerging requirements 
and how micro-services fits the bill in the current context. For now, we will capture some aspects in focused paragraph. 

* What is microservice?
* What is microservice not?
* Monoliths vs. Micro-services
* Microservice precursors



### What is microservice? 


Microservice is a fine grained atomically deployable service accessed by platform agnostic network API. It's implicit or explicty focus
is on solving one portion of problem. It is desiged to follow SRP (Single Responsibility Principle) religiously. It's lifecycle 
is completely independent and is expected scale up and down at its own will. At the same time, it expects an exo-system where it thrives
by collaborating with othere microservices in safe, secured and transaparent manner. It is like a nuclear family in our society. The below picture try to convey microservice and its eco-system.

![Microservice Ecosystem](/micro-services/images/microservice/microservice-ecosystem.jpg)

### What is microservice not?

It is not the only architecture style though its popularity has gone up by leaps and bounds in recent days. It is not supposed 
or expected to be applied everywhere. There are many other architectural patterns which is not going to go away. For e.g. 
monolithic systems for equity transaction as the focus here is of getting low latency more than anything else.

> **Microservice is a only one of architecture style among many! You apply ONLY where it fits.**

### Monoliths vs. Micro-services

Django kind of application relying on MVC pattern assumed layered architecture. It did apply separation of concern
by segregating user layer, business layer and persistence layer. But, all business layer was embedded in one place.
So, if there is any scale required in some portion of business layer, entire model needs to be changed. That's why
these are type of applications are called as monoliths application. 

Micro-services are different. They divide a domain into of set of functional unites which can interact with each 
other. A good architect needs to understand this and should be able to create a design which address the following 
aspects:

* XYZ scale-out mechanism.
	* Scale out by adding minions
	* Scale out by segregating functionalities and distributing load
	* Scale out by data partitioning 
* services are neither fat nor thin
* ability to function a service without depending on each other 
* divided around domain - a very fundamental need for designer to understand it

Be careful when we talk of scatter gather or MVC vs. micro-services patterns.


### Microservice precursors

Microservice did not emerge out of sudden. Or, implementing software as service is not a very new concept. In fact, SOA took 
its dominancein era of 2000s. From that time frame to know, computing has seen a signficant increase in peneteration of software 
in every corner of life, scale requirement and heterogenity in consumer. The below diagram illustrates how current world 
microservice has evolved to use positive aspects of its precursors.

![Microservice Evolution](/micro-services/images/microservice/microservice-evolution.jpg)



# Architecture, Design and Implementation aspect(s)

See [here](microservice-patterns.md)



# 12-factor apps

Web applications has beeon one of precursor of micro-service. There is a lot of postive influence of web application on
miroservice but it is very important to understand that microservice is not necessarily a web application. 


> ### Peer comparison
>
> Like OpenStack is an alternate for AWS for IaaS, the same goes between Heroku and Cloud Foundary as far as deevlopment of
> web application is concerend. 
>


> ### Microservice vs. Web application
> 
> The web app is always designed to face world wide web but not it does not always apply for microservice. Also, there 
> is a fundamental difference in commnication among web applications. Web applications always follows North-South traffic if
> they have to talk to each other. In case of micro-services, the internal communication is mostly East-West traffic.

12-factor app is meant for web app. So, everything applicable to web app does not apply for microservice. 
By webapp, we mean by Heroku app here. Do not read the difference as hard difference between but as a subtle variation or flexibility 
in case of microservice (like CaaS) as every container is not a web-app.

>  --------------------------------------------------------------------------------------------------------------------------------	
> ## 12 Factor rules
>  --------------------------------------------------------------------------------------------------------------------------------	
>> ### Codebase 
> - Each deployable app is tracked as one codebase in revision control.
> - Same for microservice and webapp
> - Push code for Heroku and application gets deployed.
> - Push code in Kuberentes and set of actions takes place to deploy new container.	
> --------------------------------------------------------------------------------------------------------------------------------	
>> ### Dependencies
> - Rule: An app explicitly declares and isolates dependencies via appropriate tooling (e.g. Maven, Bundler, NPM) rather than 
>   depending on implicit dependencies in its deployment environment.
> - Difference between microservice and webapp
> - In case of PaaS, as you push the code, it builds the code. While in case of CaaS, you push container images. In other words,
>   container images is the unit and use same for integration testing, governance issuse, license checks before deploying it but 
>   PaaS does not emphasize so much of immutable images. Build once is the mantra of container but not of PaaS.
> --------------------------------------------------------------------------------------------------------------------------------	
>> ### Config
> - Rule: Configuration or anything is likely to differ between deployment environment is injected bia operating system 
>   level environment variables.
> - Difference between microservice and webapp
> - Microservice rely principally on dynamic discovery mechnaism (DNS etc.) for environmental variations. On the other hand,
>   PaaS environment is little bit strong headed in usage of environment variable. So, usage of configmap, secrets etc are 
>   common pattern in CaaS but not in the case of PaaS
>  --------------------------------------------------------------------------------------------------------------------------------	
>> ### Backing services
> - Rule: Backing services such as databases or message brokeres are treated as attached resources and consumed identically acroos
>   all environment
> - Same for microservice and webapp
>  --------------------------------------------------------------------------------------------------------------------------------	
>> ### Build, release and run	
> - Rule: The stages of building an artefact, combining that artefact with configuration and starting one ore 
>   more processes from that artefact/configuration combination are strictly separated.
> - Different for microservice and webapp
>  --------------------------------------------------------------------------------------------------------------------------------	
>> ### Processes
> - Rule: The app executues as one or more statelss process.
> - Same for microservice and webapp
>  --------------------------------------------------------------------------------------------------------------------------------	
>> ### Port bindings
> - Rule: The app is self-contained and exports any/all services via port binding (including HTTP)
> - Differenct for microservice and webapp
> - Containerized microservices can listen on any port they desire and benefit from consistent port usage across replicas
>  --------------------------------------------------------------------------------------------------------------------------------	
>> ### Concurrency
> - Rule: Concurrency is usually accoomplished by scaling out app processes horizontally (though processes may also multiplex
>   work via internally maanged thread if desired)
> - Same for microservice and webapp
>  --------------------------------------------------------------------------------------------------------------------------------	
>> ### Disposability
> - Rule: Robustness is maximized via process that start up quickly and shut down gracefully, there aspects allow for rapid 
>   elastic scaling, deployment of changes and recovery from crashes.
> - Same for microservice and webapp
>  --------------------------------------------------------------------------------------------------------------------------------	
>> ### Dev/prod parity
> - Rule: Continuous delivery and deployment are enabled by keeping development, staging and production environments as 
>   similar as possible.
> - Same for microservice and webapp
>  --------------------------------------------------------------------------------------------------------------------------------	
>> ### Logs
> - Rule: Rather than managing logfiles, treat logs as event streams, allowing the execution environment to collect, aggregate,
>   index and analyze the events via centeralized services.
> - Same for microservice and webapp
>  --------------------------------------------------------------------------------------------------------------------------------	
>> ### Admin processes
> - Rule: Administrative or management tasks such as database migrations are executed as one-offf processes in environments 
>   identical to the app's log-running processes.
> - Same for microservice and webapp
>  --------------------------------------------------------------------------------------------------------------------------------	


# Cloud native apps   

After 6-7 years of conflicting and diverging conversion, we have come to common agreement what a cloud native application is or at least
to say what its chracteristics are. It's characteristics resemble like 12-factor app but it not exactly same. It is also not just about
a typical conversation around Pet vs. Cattle. PET as nurtured and take care as it goes wrong while Cattle are instantiated as needed and 
thrown as its usage is done. A cloud native application is all about achieving resiliency, observability, agility, scalability unlike 
traditional applications. These attributes are amalagation of positiveness accumulated from the learnings of 12-factor app, Pet vs. 
Cattle, microservices etc. The below picture convey same aspects.

![Cloud native application attributes](/micro-services/images/microservice/cloud-native-application-attributes.jpg)

You will find more details at CNCF foundation site. 

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



# Best practices 

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

The below quote is closer to me whenever I go for orthogonal new environment because it gives me resilience and motivation to
work hard even when my body does not support me.

> ### Every NEW things come with sorrow and pleasure but former tendds to come first.

I think that it applies to microservices. Can you identify some by looking at below picture.

![Microservice new challenges](/micro-services/images/microservice/microservice-challenges-1.jpg)

> ### Here are few! 
> ### Can you spot startup? A plenty of opportunity to innovate, isn't it?

![Microservice challenges](/micro-services/images/microservice/microservice-challenges-2.jpg)






