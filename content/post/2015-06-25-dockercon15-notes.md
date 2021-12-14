---
categories:
- posts
date: "2015-06-25T00:00:00Z"
tags:
- notes
- docker
- aws
- aws ecs
title: DockerCon 2015 Notes
---

I was lucky enough to be able to attend
[DockerCon 2015](http://www.dockercon.com/) in San Francisco this year.
With over 2100 attendees it's one of the biggest tech conferences I've
been to and the first one outside of Australia. I learned a lot while I
was there so I wanted to share my notes from the conference.

NOTE: As of 2015-05-26 This is still somewhat of a draft and may be updated

Resources
=========

If you want to watch the talks yourself, here's some links to the videos
and slides.

* [DockerCon 2015 Keynote
  Videos](https://blog.docker.com/2015/06/dockercon-2015-keynote-videos/)
* [DockerCon 2015 Videos: Day 1 of Docker, Docker, Docker](https://blog.docker.com/2015/06/dockercon-2015-videos-day-1-of-docker-docker-docker-2/)
* [DockerCon 2015 Videos: Day 2 of Docker, Docker, Docker](http://blog.docker.com/2015/06/dockercon-2015-videos-day-2-docker-docker-docker/)
* [DockerCon 2015 Videos: Day 1 of Advanced Tech](http://blog.docker.com/2015/06/dockercon-2015-videos-day-1-advanced-tech/)

Day 1
=====

General Session
---------------

The key things out of the general session that started the day were:

* Docker itself is going through rapid growth in adoption and use.
  There were some very large growth numbers for things like the number
  of jobs mentioning Docker and the use of Docker hub.
* The Docker team have big ambitions around using containers and Docker
  to make development easier. Broadly this meant making building
  systems that could be distributed (in both "deploying on multiple
  nodes" and "packaging in an easy to use manner" senses) much easier.
* There's now [experimental releases of Docker](https://blog.docker.com/2015/06/experimental-binary/) available to download for those that want to be
  on the "bleeding edge".
* A [new networking system](http://blog.docker.com/2015/06/networking-receives-an-upgrade/) has been added to Docker. This system allows for multi-host networking using [VXLAN](https://en.wikipedia.org/wiki/Virtual_Extensible_LAN) tunnels to link containers on different machines. It also enables service discovery and advertising using DNS. Note that this feature requires a backing KV store (like [Consul](https://consul.io/)] to be deployed.
* A [plugin
  system](http://blog.docker.com/2015/06/extending-docker-with-plugins/)
for Docker that initially allows for the network and volume systems to
be extended. The plugin interface is fairly limited so far, but already
plugins can be added to enable some neat tricks like live container
migration including volumes and data.
* Deepak Singh from Amazon announced that the EC2 Container Service (ECS)
  will support Docker Swarm and Docker Compose later this year. This
  is a big announcement for me as I'm spending a lot of time with ECS
  lately and native Docker tools support would be a great help. It will
  also make migrating between cluster technologies (e.g. ECS to Mesos)
  much easier.
* Signed data support with [Docker
  Notary](https://github.com/docker/notary). The Notary system provides
a way to sign and verify content including Docker images and updates. It
differs from PGP/GPG in that it is more optimised for handling updates
and provides simpler handling of key revocation in case of compromise.
* Probably the biggest news of the conference was that large parts of
  Docker are to become an open standard. The standard, called the Open
  Container Format, will be maintained by the Linux Foundation under
  the [Open Container Project](https://www.opencontainers.org/) banner.
  Docker Inc has donated a container runtime called [runC](http://runc.io/)
  which will be the reference implementation. There are a huge number
  of companies which are all participating in this standards effort
  so it seems the possible standards war with
[CoreOS](https://coreos.com/) and
[rkt](https://github.com/coreos/rkt) has been avoided.


Faster, Cheaper and Safer: Secure Microservice Architectures using Docker
-------------------------------------------------------------------------

[Adrian Cockcroft's](https://twitter.com/adrianco)
[presentation](http://www.slideshare.net/adriancockcroft/faster-cheaper-safer)
covered how best to securely deploy containers. The first thing would be
to read [Docker's security page](https://www.docker.com/docker-security)
which covers best practices when securing Docker deployments. Other
things of note:

* Keep your containers thin and single purpose. This reduces the attack
  surface and makes auditing easier.
* Add automated pen testing to your build process
* Aim for immutable services with state stored elsewhere.
* For really secure systems, deploy a single container on a virtual
  machine to get the isolation and security benefits of both.


Reliably shipping containers in a resource rich world using Titan
-----------------------------------------------------------------

[Diptanu Choudry](https://twitter.com/diptanu)
[presented](https://speakerdeck.com/diptanu/reliably-shipping-containers-in-a-resource-rich-world-using-titan) a description of how Netflix is deploying
Docker containers on a system they call Titan. It's a PaaS system
built using Mesos and other Netflix libraries. It started out just
running cron jobs and grew from there. They expect to make it open source
later this year. Other interesting bits:

* Volume management makes use of ZFS snapshots and replication
* Each container in a VPC gets a unique IP address. An ENI is created
  and moved into the container namespace.
* Container (cgroup) metrics are published to [Atlas](https://github.com/Netflix/atlas/wiki/Overview).
* Uses
  [Eureka](https://github.com/Netflix/eureka/wiki/Eureka-at-a-glance) for service discovery and advertising.
* For container [IAM](http://aws.amazon.com/iam/) roles, each AWS API call goes through a security
  proxy that verifies the source IP (since each container has an IP) and
  container is authorized to use that role. Uses [STS](http://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html) to generate temporary
  credentials.


Docker Plugins
----------------------

This was a combination
[presentation](http://www.slideshare.net/weaveworks/dockercon-plugins-session) from some of the key people leading
the plugin implementation in Docker: [Jeff
Lindsay](http://progrium.com/blog/), [Luke
Marsden](https://twitter.com/lmarsden), and
[Alexis Richardson](https://twitter.com/monadic). The title was originally a secret as the plugin
feature was only announced at the keynote.

* Major functions of Docker will become pluggable though currently only
  networking and storage are completed. Logging coming soon.
* Plugins are separate processes that are talked to using JSON over HTTP
  via a domain socket. This enables plugins to be written in any
  language.
* Since plugins are processes, they can also be containers which
  simplifies deployment.
* Demonstrated using network
  ([Weave](http://weave.works/docker-plugins/index.html)) and volume
  ([Flocker](https://docs.clusterhq.com/en/1.0.1/labs/docker-plugin.html))
  plugins to enable live container migration. You can try
  [the demo](http://plugins-demo-2015.github.io/) on your machine.


Enabling Microservices @ Orbitz
-------------------------------

Another combo
[presentation](http://www.slideshare.net/Docker/dockercon-sf-2015-enabling-microservices-orbitz) from [Steve Hoffman](https://twitter.com/bacoboy) and [Rick Fast](https://twitter.com/tortiepoint) of Orbitz. They talked about Orbitz journey to enable fast, simple deployment of microservices while keeping the existing process as far as possible. The end result is a mostly automated platform using Jenkins to coordinate actions and deployments to a Mesos cluster in their datacenter.

* Use Chef to build the base machines including Docker, Mesos, and
  Consul.
* Uses [Consul](https://consul.io/) for service discovery and advertising.
* [Marathon](https://mesosphere.github.io/marathon/) is used to deploy
  Docker container applications on Mesos.
* HAProxy cluster used to provide access to the applications deployed on
  Mesos. Uses [Bamboo](https://github.com/QubitProducts/bamboo) to
  keep the HAProxy configuration in sync with Mesos.
* Before deploying to Mesos, change notification tickets are
  automatically created in ServiceNow by Jenkins. Once the deploy
  completes the ticket is updated with the outcome.


Docker Networking
-----------------

Since Docker acquired Socketplane they've been working hard on
[refactoring the networking](http://www.slideshare.net/Docker/dockercon-sf-2015-networking-breakout-49801112) component of Docker. Madhu Venugopal and Jana
Radhakrishnan presented a [breakout
session](http://www.slideshare.net/Docker/dockercon-sf-2015-networking-breakout-49801112) on the new networking stack.

* Implemented as a separate library called `libnetwork` which
  can be used by other projects needing network functions
* Models a Container Networking Model (CNM) consisting of Endpoint, Network,
  and Sandbox.
* Endpoints are service connections. They're separate from containers
  and have a life cycle of their own.
* Networks are collections of endpoints that can communicate.
* A sandbox is a group of endpoints that can be attached to a container.
  They provide an abstraction over the concept of a container.
* Drivers are used to actually talk to the operating system network
  stack. Multiple implementations available with Docker 1.8 including a
  default "bridged network" driver for backwards compatibility.
* Overlay network driver (using VXLAN tunnels) will also be provided for
  multi-host networking.
* Service discovery and advertising is built in to `libnetwork` as well.
  It uses DNS to provide this feature in Docker 1.8. Future releases
  will make this a feature that plugins can implement to provide
  alternatives 
* Requires a shared datastore (for example Consul) that all Docker hosts
  can see to coordinate networks.


Day 2
=====

General Session
---------------

* Started with a walk through of how [Business
  Insider](http://www.businessinsider.com/) used Docker to decrease
  their release cycle time. They used their existing Puppet infrastructure
  to deploy and manage Docker containers as services. Docker allowed the
  dev and ops teams to work together better without having to radically
  change their processes.
* [Docker Hub](https://hub.docker.com/) has seen huge growth in use over
  the last year. Docker is working on improving the experience,
  increasing the security and performance at the same time. A beta of a
  new UI is [available](https://hub-beta.docker.com/).
* One of the most popular images on Docker Hub is the Docker registry
  itself. The Docker team is now making a commercial, supported version
  of the registry called [Docker Trusted
Registry](https://blog.docker.com/2015/06/docker-ready-for-production/).
  This commercial version of the registry supports enterprise features
  like LDAP authentication, role based access control, and auditing.
* Docker Inc. now offers commercial support for Docker deployments.
  Prices start from $150/month and include a license for the Trusted
  Registry.
* Microsoft is integrating Docker support in to its development tools,
  operating system, and cloud platform. Windows applications will be
  able to be containerized and deployed to Windows Server hosts as well.
* Finally a demonstration of a future product labelled Project Orca.
  This is a web UI that brings together all of the Docker tools like
  machine, compose, and swarm. You can administer clusters of Docker
  engines and the applications running on them from a single UI. It's
  still mostly a concept at this point, early signup is available at
  http://bit.ly/project-orca

The distributed system toolkit: Container patterns for modular distributed system design
--------------------

[Brendan Burns](https://twitter.com/brendandburns) from Google's Cloud Platform team
[presented](http://www.slideshare.net/Docker/slideshare-burns) on
different ways to think about system design with containers.

* Treat containers like classes in an object oriented system.
* Compose systems out of multiple, reusable, single purpose containers.
* The container is natural boundary for teams and allows you to use
  expert knowledge everywhere and only update the bits that need
  updating.
* Ensure containers are parameterized and that they are documented.
* Sidecar containers let you compose multiple functions together to form
  a new application. The sidecar can extend and enhance existing
  functions.
* Ambassador containers act as an intermediary between an application
  container and another service. This extra level of indirection allows
  for more features to be added without the application container being
  aware.
* Adapter containers interact with the application container to adapt it
  to its deployed environment. For example an adapter container might
  extract metrics from the application container and publish them.


Scaling New Services: From Container Creation to Automated Deployments
----------------------------------------------------------------------

Presented by [Brian Scott](https://twitter.com/brainscott) and [Patrick O'Connor](https://twitter.com/dontrebootme) from Disney this
[presentation](http://www.slideshare.net/Docker/docke) largely covered similar ground to the Orbitz one. Again
Mesos, Marathon, Chef, Docker, Jenkins, HAProxy, and Consul work
together to provide a PaaS like system for automated deployments. 


Container Hacks and Fun Images
------------------------------

[Jessie Frazelle](https://twitter.com/frazelledazzell) from Docker Inc.
demonstrated some atypical uses of Docker containers. With some care you
can deploy GUI/TUI applications [as containers on the desktop](https://blog.jessfraz.com/post/docker-containers-on-the-desktop/). Even ones you
wouldn't think of like Skype or Chrome.


Running Aground: Debugging Docker in Production
-----------------------------------------------

[Brian Cantrell](https://twitter.com/bcantrill) was probably the most engaging presenter there. His
[presentation](http://www.slideshare.net/Docker/day-2-3-25-405running-aground-debugging-docker-in-productionbryan-cantrill)
on debugging Docker in production covered a lot of the potential
pitfalls and possible workarounds.

* Docker and microservices are an embodiment of the Unix Philosophy
* Microservices means that you are developing a distributed system even
  if you think it isn't.
* Distributed systems are hard to debug, you need to think of debugging
  systems not instances.
* Failures can be broadly categorized in to Non-fatal/Fatal and
  Implicit/Explicit.
* Fatal failures are easier to debug especially if you can capture the
  in-memory state at the time of failure, e.g. a core dump. Docker lacks
  a core dump feature but it is coming.
* Non-fatal explicit failures require more runtime tools like logs or
  instrumentation to diagnose. Debugging these typically requires things
  like log analysis.
* When deploying with containers, try very hard not to manipulate the
  Docker host in any way. As soon as you do, those Docker hosts become
  compromised and many assumptions about immutability and security are
  broken. If you need something from the host, make sure it's shipped
  somewhere else for safety and easy of access.
* The ability to debug and diagnose production problems is essential for
  Docker deployments to succeed.


Final General Session
---------------------

I don't have too many notes from this, but I do remember one of the more
impressive final demonstrations was the live migration of a Quake 3
server while players were connected. You can watch it here
https://www.youtube.com/watch?v=mL9AFkJJAq0

