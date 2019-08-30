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

# Identifying API endpoints

```go
/data-center/
/data-center/<dc-id>/storage-system/
/data-center/<dc-id>/storage-system/<ss-id>/datastore
/data-center/<dc-id>/storage-system/<ss-id>/datastore/<ds-id>
/data-center/<dc-id>/storage-system/<ss-id>/datastore/<ds-id>/volume
/data-center/<dc-id>/storage-system/<ss-id>/datastore/<ds-id>/volume/<volume-id>
/data-center/<dc-id>/storage-system/<ss-id>/datastore/<ds-id>/volume/<volume-id>/datasets
/data-center/<dc-id>/storage-system/<ss-id>/datastore/<ds-id>/volume/<volume-id>/datasets/utilization
/data-center/<dc-id>/storage-system/<ss-id>/datastore/<ds-id>/volume/<volume-id>/datasets/events
/data-center/<dc-id>/storage-system/<ss-id>/datastore/<ds-id>/volume/<volume-id>/datasets/performance
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

## Goals

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

## Users

The system functionality should be accessible to following set of users.

	1. Super admin. He or she should be able to access all API endpoints.
	2. All authenticated users. He or she should be able to access API endpoints which is meant for common users only.
	3. Analytic admin. He or she should be able to access API endpoints providing insights into datasets but should not be provided with infrastructure details.
	4. Operation admin. He or she should be able to perform all operation related activities but should be prevented for access of some sensitive datasets and its analytics.


## Iterative design 

To make the subject more explicit in understanding, we will follow iterative approach to conclude on acceptable design. This 
section talks about follownig iterations.

	1. Simplistic design
	2. Separating business logic from cross cutting concerns 
	3. Granualrizing services 
	4. Easy to use facade to simplifiy client interaction
	5. Support loadbalncing
	6. Support high perofrmant API using cache
	7. Support canary release ecosystem

### Simplistic design
### Separating business logic from cross cutting concerns 
### Granualrizing services 
### Easy to use facade to simplifiy client interaction
### Support loadbalncing
### Support high perofrmant API using cache
### Support canary release ecosystem


# Deployment strategy


# Release 

