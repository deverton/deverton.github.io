---
title: Experimenting with Terraform, Consul, and Amazon EC2
layout: post
category: posts
tags: [development, terraform, consul, ec2, amazon]
---

One of the things I've been experimenting with is
[Terraform](http://www.terraform.io/) from Hashicorp. It provides a
simple configuration language for describing infrastructure that fills
in the gap left by configuration tools like Puppet, Salt, and Ansible.
Those tools can only describe what happens after a machine is created,
not how to create that machine.

I thought I'd try and create the beginnings of a typical production test
environment with split public and private networks. None of these
networks are visible outside of the VPC. Instead a bastion host is used
that also doubles as the NAT gateway.

To simplify service discovery I also deployed Consul to act as the DNS
provider for hosts within the VPC. I started this work before Route 53
supported split-horizon DNS so I'm not sure if you still need to use
Consul for this.

You can see the result of the experiment on my
[terraform-aws-consul](https://github.com/deverton/terraform-aws-consul)
respository on Github. It should work out of the box with Terraform
0.3.5 which was the latest at time of writing. The readme in that
repository should cover the details of how to get going. Only
``t2.micro`` instances are used so it should cost you nothing (or very
little) to deploy.

There's a few things I had to figure out along the way that weren't
particularly obvious. The trickiest was getting the cloud-init files for
userdata in to the right MIME multi-part format and keeping them in-sync
with the component files. I hadn't written a Makefile in years but it
neatly solved the problem of running ``write-mime-multipart`` when
needed.

Another trick was realising that you can tag instances in EC2 with
``Name`` and that will control the instance name on the EC2 dashboard.
This was particularly handy for the Consul member nodes to make them
easy to identify as ``consul1``, ``consul2``, etc.

The final trick was getting the DHCP settings sorted for Amazon Linux so
that the instances would still get IPs from DNS, but have a
``resolv.conf`` modified to use a local Consul instance for DNS. This
turned out actually be supported by modifying the ``dhclient``
configuration in cloud-init.

While this experiment doesn't actually do all that much in the end, it
seems like a good starting point. From here I'd like to actually deploy
some applications and have them register with Consul. In a different
experiment I've used [Mesos](http://mesos.apache.org/) and
[Marathon](https://github.com/mesosphere/marathon) for this, but that's
another post.

