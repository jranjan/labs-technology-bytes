# Building microservices

# Problem statement

Our company is very large bioscience company and is involved in cutting research like genetic engineering.
Our need is to have ability to realize end-to-end data lifecycle management. The size of data can be in order 
of PB and can vary in nature. Some are telemetry data. Some are very sensitive data. Some are for record
purpose generated using clincial trials. Need to have a IT platform which can support storage system of 
different vendors with ability to provide insights into data. At this point of time, we are primarily looking 
for insights into utilization, performance, health, events but will very soon exxtend these with other 
analyic based on genetic science like which age group of patients are more like to get asthma based on their
historical health record. It is very important for proper authentication and authorization to be applied
while accessing any behavior.


# Domain Driven Design

In this section, we will try to sketch high livel model which defines problem and solution aspects of above
problem with a closer to accuracy. The model will thrive to accurate representation and hence should not be 
confused with ER diagram. It's purpose is to establish a common vocabulary and its usage to define a model
which can be used during communication with various stakeholders.  To build a domain model, it requires a 
lot of interaction with domain experts and model engineers. This section is not about domain driven design
and hence we will not focus on how to identify entity, how to indetify relationship, how to discard less 
useful entity to avoid cluttering etc. Let us assume that, we arrived at following model depicted below.

![Domain model](/micro-services/images/genetic-domain.jpg)


# Identifying API endpoints

```
> /data-center/
> /data-center/<datacenter_id>
>
> /data-center/storage-system/
> /data-center/storage-system/<storage_system_id>
> /data-center/storage-system/<storage_system_id>/telemetry
> /data-center/storage-system/<storage_system_id>/volume
> /data-center/storage-system/<storage_system_id>/volume/<volume_id>
> /data-center/storage-system/<storage_system_id>/volume/<volume_id/telemetry
>
> /data-center/storage-system/datastore
> /data-center/storage-system/datastore/<datastore_id>
> /data-center/storage-system/datastore/volume
> /data-center/storage-system/datastore/<datastore_id>/telemetry
>
> /data-center/storage-system/dataset
> /data-center/storage-system/dataset/<dataset_id>
> /data-center/storage-system/dataset/<dataset_id>/policy
> /data-center/storage-system/dataset/<dataset_id>/telemetry
>
> /data-center/storage-system/analytic
> /data-center/storage-system/<entity_type>/event
> /data-center/storage-system/<entity_type>/utilization
> /data-center/storage-system/<entity_type>/performance
```


# Identifying microservices

As we know, microservices is an architecture style. So, there is not a stone-casted rules for implementing a 
large system using set of microservices. You can have 1 microservices doing every thing or many in order of 10s.
It all depends upon the business requirement which is partially explict and partially implicit. The challenge in 
designing software system is to represent explcity aspects with clarity, discover implicit aspects as much as 
possible. At the same time, the focus should be on architecting and design a system which is extensible, scalable
and performative. This is the place where three things matters: Knowledge, Wisdom and Experience. By experience,
we do not mean by number of age. In this section, we will try to build system which is loosely coupled and granular
enough to be owned by a team of 3-9 person.

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


> ## Iterative design 


To make the subject more explicit in understanding, we will follow iterative approach to conclude on acceptable design. This 
section talks about follownig iterations.

1. Simplistic design
2. Separating business logic from cross cutting concerns 
3. Granualrizing services 
4. Easy to use facade to simplifiy client interaction
5. Support loadbalncing
6. Support high perofrmant API using cache
7. Support canary release ecosystem

#### Simplistic design

![Simplistic design](/micro-services/images/genetic-service-design-1.jpg)

#### Separating business logic from cross cutting concerns 

![Seperating cross cutting concenrs](/micro-services/images/genetic-service-design-2.jpg)


#### Granualrizing services 

Let us embed scale by using segregating responsibilities. This will not only help to scale specific segment to scale independently but 
will also promote loose coupling, independent deploymnt and update, granular execution of service implementation, ability to use 
different technology for different set of functionalities if desired. In this case, we will decompose the system in the following 
microservices:

1. **datacenter.** The service is responsible for user operation related to datacenter. It uses its indepndent database to store 
   entities related to datacenter. The entities can be server, storage, network on physical infrastructure side. The entities can 
   be hardware management software like HPE OneView. Or, the entities can be public cloud virtual infrastructure. For simplification 
   of discussion, we will focus that service provides interface which allows us to perform CRUD operation on datacenter (a 
   logical entity).
2. **datstore.** The service is responsible for user operation related to datastore. It uses its independent database to store 
   entities comprising datastore i.e. aset of volumes as well as maintaining datasets created for it. A datastore can be partitioned 
   into multiple datasets or buckets for specific purpose as needed. For simplification of discussion, we will focus that 
   service provides the interface which allows us to perform CRUD operation on datastore (a logical entity).
3. **volume.** The service helps in attaching a volume to datastore as and when needed to scaleout capacity of datastore.
4. **datasets.** The service is responsible for use to allow paritioning of datastore into set of datasets of buckets, each of which can
   be used to store data of specific purpose. It does not have its own databased but perform algorithm operations like partitioning
   the datastore, setting storage policy etc.
5. **events.** The service represents a sepcific microservice which allows user to perform set of operation related to events 
    performed on datasets. It helps in enumerating who performed what and when with whom. It uses specific database like 
    nfluxDB for storing trends.
6. **utilization.** The service represents a sepcific microservice which allows user to determine capacity related metrics 
    associated with datasets. It uses specific database like InfluxDB for storing trends.
7. **perofrmance.** The service represents a sepcific microservice which allows user to determine performance related
   metrics associated with datasets. It uses specific database like InfluxDB for storing trends.



![Segregation of responsibilities](/micro-services/images/genetic-service-design-3a.jpg)

> Let us represent same using usage of microservice vocabulary.

![Microservices for specific bounded context](/micro-services/images/genetic-service-design-3b.jpg)

> Let us determine the interaction among microservices with high level constituents. 
> As you can see from below diagram that wee did not use independent database for each service so that they are loosely coupled. 
> Also, some of the services are completely stateless as they do not need any persistence.

![Interaction among microservices](/micro-services/images/genetic-service-design-3c.jpg)

> ### Hey, we used functonal decomposition to promote scaling implicitly. Did you notice that?

#### Easy to use facade to simplifiy client interaction
#### Support loadbalncing
#### Support high perofrmant API using cache
#### Support canary release ecosystem



# Deployment strategy


# Release 

