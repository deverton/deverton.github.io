---
title: Amazon EC2 Container Service Wishlist
layout: post
category: posts
tags: [aws, ecs, docker]
---

I've spent a lot of time with Amazon's [EC2 Container
Service](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html)
lately. While the service works well for the basic use case of deploying
Docker containers in a cluster, there's quite a few missing features
that would make life easier if Amazon implemented.

The lack of IAM instance profiles for containers is probably the biggest
issue for us. Without them, we have to go back to embedding access keys
inside the application which introduces the whole mess of securely
handling secrets. The upside is that it's forced us to look in to things
like [Vault](https://vaultproject.io/) which might provide a better long
term solution anyway.

The other big sticking point is the complete lack of any autoscaling
support, either for container instances or for service task counts.
Ideally you'd have the ability to have the container instances scale
up if tasks can't be placed due to a lack of resources. Services should
also be able to scale up based on their usage of their resource
allocations. To be fair you can sort of cobble together something with
CloudWatch metrics, SNS, and a Lambda function but this is the sort of
feature you'd expect to be built-in from the start. 

We've also had to build a fairly elaborate set of tools on top of ECS to
allow us to do pre-release testing of service deployments. I know that
ECS supports seamless releases, but sometimes you want to just check
the deployed service is working *before* you cut the ELB listeners over
to the new instances. More control over the release process would help a
lot here.

It would also be nice to have more control over placement of tasks in
services. To allow, for example, to say 'this task needs to be on
machines in this AZ', or 'this task needs to be on machines with lots of
IO'. You can probably work around this a bit by creating different
clusters for different placements, but that gets very expensive fast and
kind of defeats the purpose of a general cluster scheduler.

When things go wrong in ECS it's also quite hard to get visibility of
the logs. There's many moving parts (ECS Agent, Docker itself, the
service) required to get an ECS service up and it can be very hard to
tell which bit is having problems.

There's also a bunch of nice to have features I'd like to see like:

* integration of EBS volumes as Docker volumes
* any sort of tagging support of services or tasks
* periodic execution of tasks (e.g. cron style). The infrastructure is
  all there, it just needs the timer part

Here's hoping that Amazon adds these features soon. AWS re:Invent is
coming soon, maybe we'll see something there?

