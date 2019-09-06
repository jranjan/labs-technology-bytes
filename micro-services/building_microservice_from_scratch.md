# Content

1. [Introduction](#introduction)
2. [Problem statement](#problem)
3. [Domain Driven Design](#domain-model)
4. [Identifying API endpoints](#api)
5. [Identifying system goals and users](#goal-and-user)
6. [Identifying microservices](#identify-service)
     * [Simplistic design](#monolith-system)
     * [Granualrizing services](#monolith-system-concern-separation)
     * [Easy to use facade to simplify client interaction](#)
     * [Support loadbalancing](#loadbalancing)
     * [Support high perofrmant API using cache](#cache)
     * [Support canary release ecosystem](#canary-release)
7. [Code, Build and Release](#devops)
8. [Deployment strategy](#deployment)
9. [Summary](#summary)
10. [Next](#next)
     * [Challenges](#next-address-challenges)
     * [Related aspets](#collaborators)



# Introduction <a name="introduction">

Let us take a problem statement to design a system which follows microservice architecture style. As we progress on discussion, we will 
try to solve various pieces of puzzle which a truly scalable, resilient and easy to manage system should have. It is not possible to 
cover all corner of a microservice in a single document but will try to address following aspects:

* Defining a **domain model** representing system
* Identifying microservices
* Segregating cross cutting concerns so that a single microservice focus completely on its buisness logic.
* Simplifying user interaction with system
* Auto-scaling and using loadbalancer to share the load across multiple instances of services
* Leveraging caching logic to create high-performant service
* Supporting canary release model
* Repository organization for individual microservice
* Various deployment strategy to suite the system need as and when required

It is assumed that reader is familar about microservice, DevOps, container etc at basic level. 

Before we proceed, here is notable points:

> Microservice is an architecture style like others. It is not a replacement for all other architecture style. 
> It requires a change in mind shift and cultural change inside team whose accentuated benefits are seen if it is adppted 
> alongwith with DevOps and Container ecoysystem.

# Problem statement <a name="problem">

Our company is a very large bioscience company and is involved in cutting research like genetic engineering. Our need is to have 
ability to realize end-to-end data lifecycle management. The size of data can be in order of PB and can vary in nature in terms of
governance, performance, capacity, accessibilty etc. Some are telemetry data. Some are very sensitive data. Some are for record 
purpose generated during clincial trials. We need to have an IT platform which can support storage system of different vendors 
with ability to provide insights into data. At this point of time, we are primarily looking for insights into utilization, performance,
health, events but will very soon extend these with other analyic solution. For e.g. we will like to devise an AI based solution 
which can be predict the probability of a person having critical diseases like cancer based on study of genes. Considering that 
system is likely to host sensitive data for patients, it is implicitly desired that system supports proper authentication 
and authorization wherever required. Also, the system is likely to host large amounts of data and new services are likely to be 
incepted in future, it is very desirable that system is designed for scale and easy to adopt new set of services as and when required.


# Domain Driven Design <a name="domain-model">

In this section, we will try to sketch high livel model which defines problem and solution aspects of above
problem with a closer to accuracy. The model will thrive to accurate representation and hence should not be 
confused with ER diagram. It's purpose is to establish a common vocabulary and its usage to define a model
which can be used during communication with various stakeholders.  To build a domain model, it requires a 
lot of interaction with domain experts and model engineers. This section is not about domain driven design
and hence we will not focus on how to identify entity, how to indetify relationship, how to discard less 
useful entity to avoid cluttering etc. Let us assume that, we arrived at following model depicted below.

![Domain model](/micro-services/images/building-micro-service/genetic-domain.jpg)


# Identifying API endpoints <a name="api">

```
/data-center/
/data-center/<datacenter_id>

/data-center/storage-system/
/data-center/storage-system/<storage_system_id>
/data-center/storage-system/<storage_system_id>/telemetry
/data-center/storage-system/<storage_system_id>/volume
/data-center/storage-system/<storage_system_id>/volume/<volume_id>
/data-center/storage-system/<storage_system_id>/volume/<volume_id/telemetry

/data-center/storage-system/datastore
/data-center/storage-system/datastore/<datastore_id>
/data-center/storage-system/datastore/volume
/data-center/storage-system/datastore/<datastore_id>/telemetry

/data-center/storage-system/dataset
/data-center/storage-system/dataset/<dataset_id>
/data-center/storage-system/dataset/<dataset_id>/policy
/data-center/storage-system/dataset/<dataset_id>/telemetry

/data-center/storage-system/analytic
/data-center/storage-system/<entity_type>/event
/data-center/storage-system/<entity_type>/utilization
/data-center/storage-system/<entity_type>/performance
```


# Identifying system goals and users <a name="goal-and-user">

Before we start talking about design of services, let us identify goal of our system. We should know the destination 
where we do want to reach before we plan for the vacation. Isn't it? 

> ## Goals


1. **Security**
     * It should use secured service endpoint.
     * It should be athenticated before any accessibility of functionality is provided.
     * It should allow access of specific functionality only if he or she is authorized for it.
2. **Scalability**
     * It should dynamically scale with zero to minimal human intervention.
     * It should be scalable without change in code.
     * It should provide flexibilty to scale specific piece of functionality instead of entire system.     
3. **Client accessibility**
     * Client should be provided simple view of system access instead of cluttered set of APIs
4. **Loadbalancing**		
     * It should be able to loadbalnce the workload for specific functionality as there can be different number of instances 
       running for different components.
5. **Performance**
     * It should be highly perofrmant and should leverage caching wherever possible avoid unncessary computation.
6. **Release**		
     * It should be able to release specific portion of functionality independently instead of releasing all bits of system
     * It should be able to run multiple version of services at the same time, if desired.
     * It should support continuous delivery model.

> ## Users


The system functionality should be accessible to following set of users.

1. **Super admin.** He or she should be able to access all API endpoints.
2. **All authenticated users.** He or she should be able to access API endpoints which is meant for common users only.
3. **Analytic admin.** He or she should be able to access API endpoints providing insights into datasets but should 
   not be provided with infrastructure details.
4. **Operation admin.** He or she should be able to perform all operation related activities but should be prevented for 
   access of some sensitive datasets and its analytics.


# Identifying microservices <a name="identify-service">

As we know, microservices is an architecture style. So, there is not a stone-casted rules for implementing a large system using set 
of microservices. You can have 1 microservices doing every thing or many in order of 10s. It all depends upon the business requirement
which is partially explict and partially implicit. The challenge in designing software system is to represent explcity aspects with
clarity, discover implicit aspects as much as possible. At the same time, the focus should be on architecting and design a system which
is extensible, scalable and performative. This is the place where three things matters: Knowledge, Wisdom and Experience. By experience,
we do not mean by number of age. In this section, we will try to build system which is loosely coupled and granular enough to be owned
by a team of 3-9 person. 

To make the subject more explicit and illustrative in understanding, we will follow iterative approach before we reach an acceptable
design. This aligns with agile methodologies of incremental delivery where we can implement system in simplistic form and refactor
as and when required. This section talks about follownig iterations:

1. Simplistic design
2. Separating business logic from cross cutting concerns 
3. Granualrizing services 
4. Easy to use facade to simplifiy client interaction
5. Support loadbalncing
6. Support high perofrmant API using cache
7. Support canary release ecosystem

## Simplistic design <a name="monolith-system">


![Simplistic design](/micro-services/images/building-micro-service/genetic-service-design-1.jpg)

## Separating business logic from cross cutting concerns <a name="monolith-system-concern-separation">

![Seperating cross cutting concenrs](/micro-services/images/building-micro-service/genetic-service-design-2.jpg)


## Granualrizing services <a name="granual-service">

Let us embed scale by using segregating responsibilities. This will not only help to scale specific segment to scale independently but 
will also promote loose coupling, independent deploymnt and update, granular execution of service implementation, ability to use 
different technology for different set of functionalities if desired. In this case, we will decompose the system in the following 
microservices:

1. **datacenter.** The service manages 'datacenter' entity and provides user to perform various CRUD operation. To persist its
   constituents, it uses independent RDBMS database. The consitutents can be physical infrastructure elements or software bits. 
   For e.g.:
      * Server, 
      * Storage, 
      * Network 
      * HPE OneView
      * public cloud virtual infrastructure. 
   For simplification of discussion, we will noy go too deep into its constituents.
2. **storage-system.** The service manages 'storage-system' entity and provides user to perform various CURD operation. It facilitates
   discovery of volumes for a given storage system. To presist its discovered and other information, it uses independent RDBMS database.
3. **datstore.** A datastore is logical group of volumes to provide a virtual datastore where storage space from individual volumes 
   are combined in variety of ways to offer differentiate storage offering. The service manages 'datastore' entity and provides user 
   to perform various CRUD operation. It uses its independent RDBMS database to persist variety of data like volumes forming 
   datastore etc.
4. **dataset.** A dataset is like a bucket or small portion of datastore which can be alloted to individual or set of user. The service
   manages is 'dataset' entity and provides user to perform various CRUD operation. It facilitates different storage policy to be
   applied based on user requirement. The policy can dictate auto-scalng attributes, performance SLA, governance rules etc. It uses 
   its independent RDBMS database to persist variety of data like policies.
5. **telemetry.** The service hosts set of data telemetry data generated by various services. It categorizes them and store them in 
   optimal way so that indexing and searching can be performed fast. It uses InfluDB (not RDBMS) unlike other microservices. Also, it is 
   to be noted that this service is not a user facing service.
6. **analytic.** The service allows user to carry out set of historical analytics and forecast on telemetry generated by specific 
   entities. It does not have any database and use inline processing to dervie the required result.

    
![Segregation of responsibilities](/micro-services/images/building-micro-service/genetic-service-design-3a.jpg)

> Let us represent same using usage of microservice vocabulary.

![Microservices for specific bounded context](/micro-services/images/building-micro-service/genetic-service-design-3b.jpg)

> Let us determine the interaction among microservices with high level constituents. 
> As you can see from below diagram that wee did not use independent database for each service so that they are loosely coupled. 
> Also, some of the services are completely stateless as they do not need any persistence.

![Interaction among microservices](/micro-services/images/building-micro-service/genetic-service-design-3c.jpg)

> ### Hey, we used functonal decomposition to promote scaling implicitly. Did you notice that?

## Easy to use facade to simplify client interaction <a name="adapter">

So far, so good! We are able to decompose system into fine grained independent sub-systems. Let us change the focus from system to 
client who is going to consume the services. As a client, he or she wants to develop a home portal page which comprises following 
data points:

* Overall system report
* Display of trend of utilization of admin preferred 3 datastore 
* Display of trend of performance of admin preferred 3 datastore 
* Display of top active datasets 
* Display of events for storage system, datastore and datasets

A UI developer needs to make multiple calls to fetch data from different microservices as some of those are listed below:

* Get number of storage system
* Get number of datastore for each storage system and perform additive operations
* Get number of datasets for each datastore and perform additive operations
* Fetch admin preferences
* Get utilization and performance trend for the admin choosen datastore and datasets
* Get events for datastore
* Get events for datasets
* Get events for storage system

![Dashboard](/micro-services/images/building-micro-service/genetic-service-design-4a.jpg)

As we can see that a client needs to make multiple call. Life will be much simpler if there is adapter or facade service which can 
provide simplified data by collecting from rest of service instead of making client to go and fetch individual details and assemble 
those.  Let us call that service as Adapter service. Our eco-system will look like as depicted below. 

![Dashboard](/micro-services/images/building-micro-service/genetic-service-design-4b.jpg)

## Support loadbalancing <a name="loadbalancing">

As we learnt that one of tenets of microservice is scalability. One easy way to achieve this is by creating immutable instances as 
the service gets popular and demand grows or more and more entities needs to be managed. In any case, we do not need to grow all service
togther. We can very well pick and scale model to dynamically scale the services and this process can be automated using various tooling 
already available on container management platform like kubernetes. 

Did you guess the need of extra component as we have multiple instances?
Yes, you are right. We need a loadbalancer which can route the request to specific service instance as requested is routed to it.

We can do so in two ways as depicted pictorially:

1. Usage of loadbalancer in API gateway (most commonly used pattern)
2. Usage of service specific loadbalancer instances

See diagrams below:

> ### Usage of loadbalancer in API gateway (most commonly used pattern)

![Usage of loadbalancer of API gateway](/micro-services/images/building-micro-service/genetic-service-design-5a.jpg)

> ### Usage of service specific loadbalancer instances

![Service specific loadbalancer](/micro-services/images/building-micro-service/genetic-service-design-5b.jpg)


## Support high perofrmant API using cache <a name="cache">

In today's era, ability to serve any service withing stipulated time is not only a desired activity but a critical aspect to fullfill
SLA as well as avoid chain failure because of timeout of one service. Also, it is very important for good user interaction as anything
which takes longer to respond causes the distraction and increase friction between service and its user. One of the way to solve is 
a traditional approach of using cache. Depending upon service, one can device following caching mechanism:

1. Leverage cache logic supported by API gateway
2. Usage of service centric caching technique
3. Usage of combination of (1) and (2)

> ###  Leverage cache logic supportd by API gateway

One can configure gateway to route request to micro-service only if it can not be served by gateway cache. For e.g. user
exercises requests for utilization data 10 times an hour but analytic service updates data only once in an hour. In that case, gateway
can be configured to route request to analytic service only once in hour. And, user requess will be served from local cache for gateway
raised within one hour timeframe. It significantly reduces workload on service. Isn't it?

![API gateway cache](/micro-services/images/genetic-service-design-6a.jpg)

> ###  Usage of service centric caching technique

One can configure service specific cache. It helps in two ways:

1. Faster processing of request whenever service receives it. The service understand the data pattern more closely than API gateway or
   other parts of eco-system. Henceforth, specific caching technique pertaining to service can be employed.
2. Cache logic can be applied to services which are not frontend by public interfaces but participates in realization of entire system
   through their published interface.

The cache logic need not to be part of service's buisness logic. It can be a third party component or indigeneous code 
providing functionality of caching and can be deployed as independent service unit as depicted in picture below.

![Service centric cache](/micro-services/images/building-micro-service/genetic-service-design-6b.jpg)

> ###  Usage of combination of (1) and (2)

As we discussed above, API gateway helps in reducing number of requests routed to service and thus improving the load on service. On the
other hand, service specific cache empowers it to serve the request in faster way whenever a request is routed to it. Depending upon 
the context and need, the system needs to use one or another or combination of both.

![Service centric cache and API gateway cache](/micro-services/images/building-micro-service/genetic-service-design-6c.jpg)


## Support canary release ecosystem <a name="canary-release">

Canary release is a technique that is used to reduce the risk of introducing a new software version in production by gradually 
rolling out the change to a small subgroup of users, before rolling it out to the entire platform/infrastructure and making it 
available to everybody. As we have decomposed a system into fine-grained services, it is easy to implement canary release model
as a failuer in one service is not likely to affect other parts of system till it abide by published intefaces. This release model 
has following advantages:

1. Gradual rollout of services limits the potential system blast because of operational issues
2. Gradual release of new functionality to users reduces risk of negative outcomes impacting a large percentage of your user base

Typically canary releases are implemented via a proxy like Envoy or HAProxy, smart router, or configurable load balancer. The 
releases can be triggered and orchestrated by continuous integration/delivery pipeline tooling, such as Jenkins or Spinnaker, 
automated “DevOps” platform like Electric Cloud, or automate or feature management SaaS platforms like LaunchDarkly or Optimizely.

See [canary release](https://martinfowler.com/bliki/CanaryRelease.html) to know more about it.

> ### Is canary release ONLY release model for micro-services?





# Code, Build and Release <a name="devops">


It is time to organize sevices to generate artefacts which can be deployed. 

Microservice goes very well with image based release where we do accept or discard complete image in contrast to usage of patches to
update specific bits. To keep discussion simple and illustrative, we will pick core services and focus on below microservices.

![Interaction among microservices](/micro-services/images/building-micro-service/genetic-service-design-3c.jpg)

The fundamental artifact is an container image. It is good to follow the below cardinal relationship as much as possible.

![Cardinal relationship](/micro-services/images/building-micro-service/genetic-service-cardinal-relationship.jpg)

If we apply the above concept then we might end up our repo structure something like as mentioned below.

![Repository structure](/micro-services/images/building-micro-service/microservice-repository-structure.png)


# Deployment strategy <a name="deployment">

![Work in progress](/micro-services/images/general/work-in-progress.png)


# Summary <a name="summary">

As we can see that the entire system is no more like a monolith application. It has been decomposed into fine grained microservices 
which has its own independent existence. Certainly, services are dependent on each other to realize overall functionality but they
can grow in decenteralized environment indepdently till they abide by contracts published (not necessarily public) interfaces. This 
facilitates a great deal in realizing following use cases:

* A service can be comompletely owned by one team
* A service can be developed using technology stack which suits the need of hour.
* A service can have its independent development environment: repo, CI/CD pipeline, automated test cases.
* A service can have its own release plan.
* A service can be upgraded as a sub-part of overall eco-system.
* A service can change its internal design and interaction with other services without affecting others unless and until it breaks 
  the published interface.
* A service can be scaled independently.
* A service can be deployed in totally different environment if compared to rest.

To summarize, we carried out following activites to design the system:

* Comprehending problem statement
* Creating domain model
* Defining goals
* Identifying microservices and its interface (public or published)
* Setting up code repoistory for microservices
* Setting up DevOps phases

# Next? <a name="next">

At this point of time, we are equipped to define a model for business domain, identify microservice and translate those to independent 
ownership, manageable and deployable unit. In this process, we created a many minions which is going to create a new set of problems 
in aspects mentioned below:

* Mechanism of communication among services
* Dynamic communication management
* Centeralized logging
* Loadblancing (how to achieve efficiently, not what part)?
* Monitoring
* Broader level observability of who, what, how and where aspects of services
* Need to fullfil the promise of automated release pipeline: development -> staging -> production
* Need to live in hybrid cloud ecos sytem (**this is not our created problem but an expectation**)

> ## Addressing challenges <a name="next-address-challenges">

![Microservice challenges](/micro-services/images/microservice/microservice-challenges-2.jpg)

> ## Related areas, worth to be explored <a name="collaborators">

![Microservice next](/micro-services/images/building-micro-service/genetic-service-design-next.jpg)
