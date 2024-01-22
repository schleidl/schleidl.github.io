---
layout: post
title:  "The Mental Model of the Cloud: Why Cloud Computing Is Fundamentally Different Than Traditional Data Centers"
date:   2024-01-21 21:27:38 +0200
categories: Cloud computing
---
Designing cloud-native applications requires having a mental model of how a cloud is implemented. This mental model is fundamentally different than the one used to design applications running in a traditional data center. This article shows the differences between both mental models, focusing on the network architecture part.

The image below shows a mental model of a traditional data center. The blue boxes resemble physical servers, each running four virtual machines (VMs). As a means of logically separating different servers running different kinds of applications, these servers run in different network zones. Customers can rent a physical host or single VMs residing in a specific network zone. Within one network zone, it is possible to access any VM from any other VM. Thus, customers need to implement intrusion prevention measures. There might be cases where VMs of different customers run on the same physical host.

The only way to communicate between network zones is via the firewalls between them. The firewalls refuse any messages that have not been explicitly allowed before. In order to allow a message to pass through a firewall, a firewall rule needs to be created. A firewall rule can, for example, state that all VMs on a specific physical host can pass. Firewalls can be deployed as virtual appliances or in the form of a hardware firewall.

![data center mental model](/docs/assets/images/datacenter.png)

In contrast to that, a cloud data center does not have network zones with firewalls between them. Networking is fully virtualized. Each customer can create multiple virtual network zones called Virtual Private Clouds or VPCs. An arbitrary number of resources like VMs can be put into these VPCs. In the image below, the VMs marked with a dot of the same color are assigned to the same VPC. It is possible that different VMs running on the same physical host are assigned to different VPCs.

![cloud mental model](/docs/assets/images/cloud.png)

Each customer is able to design what traffic can enter and leave a VPC. At AWS, Security Groups are the means to restrict and allow traffic to and from the resources assigned to a VPC, for example.

Additional Resources:
A white paper with details of how virtual networking is implemented at AWS using the AWS Nitro System:
https://docs.aws.amazon.com/pdfs/whitepapers/latest/security-design-of-aws-nitro-system/security-design-of-aws-nitro-system.pdf

A paper showing how AWS used formal verification methods to prove the correctness of the boot code for the AWS Nitro System:

https://link.springer.com/chapter/10.1007/978-3-319-96142-2_28