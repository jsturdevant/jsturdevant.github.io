---
title: Microservices Position
subtitle: What Are Microservices? Should We Use Them?
---

Microservices have become a really hot topic within the last couple years. [ThoughtWorks](https://www.thoughtworks.com/insights/blog/microservices-nutshell), [TechTarget](http://searchsoa.techtarget.com/definition/microservices), [O'Reilly](http://www.oreilly.com/programming/free/reactive-microservices-architecture.html), [SmartBear](https://smartbear.com/learn/api-design/what-are-microservices/), and [Martin Fowler](http://martinfowler.com/articles/microservices.html) have all written about Microservices (among others). Almost all of those referenced articles start off by saying there is no globally accepted definition of microservices. Instead they all attempt to explain the characteristics of a microservice. 

Implementing a microservices architecture certainly has both pros and cons. Additionally, there are some pre-requisites to implementing this architectural style. This article explains what microservices are - in my opinion, decribes the pre-requisites, and then attempts to make a case for using it.

# Characteristics of Microservices

In general, we can describe a microservice architecture as a way of designing applications as independently functioning, deployable, and scalable services. Microservices should be simple, focused on doing one thing and doing it really well. Usually there are no standards as to the language used to create microservices; you should use the language that makes the most sense - use the best tool for the job. Microservices should be designed for failure, be highly resilient, and horizontally scalable.

## Business Domains

Microservices should be based on business domains. Business domains can be defined in a number of ways depending on business objectives. However, we can generalize that business domains are bounds in which a microservice lives and only addresses a single domain - for example, eligibility queries

Defining business domains for microservices is an art, not a science and should be expected to change over the lifetime of a microservice.

The goal of the business domain is to create an evolvable and independent microservice.

## Monitoring/Statistics
 - Ben

## Scalable

## Designed for Failure/Resiliency

# Pre-Requisites

## Discipline

## Automated Testing - Jon

Look at Sociable unit testing (black box) and Solitary unit testing

Contract tests - Pact, Pacto, Janus

DSLs - Guage, Concordian

## Automated Releases

## Dynamic Infrastructure

## Service Virtualization (optional)

Look at moco, stubby4j, mountebank

# Pros

## Parallel Development of Services

## Independent Evolution

# Cons

## Networking Latency

# Designing Microservices

The user experience community has come up with a honeycomb they use to design user interfaces. For microservices, we should keep the same points in mind.

![UX Honeycomb](/img/ux-honeycomb.png)
From: [http://www.lisannevdl.nl/_lib/upload/Honeycomb.png](http://www.lisannevdl.nl/_lib/upload/Honeycomb.png)

The picture is setup the way it is because if you do everything on the outside, then you should create something that is valuable. We don't want to build anything that doesn't bring us value. We need to keep these same points in mind when designing microservices.

Something else to keep in mind is complexity of the microservice. Understand that everyone doesn't necessarily see things the same way you do. Complexity is perceived as a bad thing, but in reality it isn't complexity that is bad, it is confusion. Something easy for you to understand may be challenging for someone else to understand. If something is confusing, then it won't be maintained and therefore won't bring us value.