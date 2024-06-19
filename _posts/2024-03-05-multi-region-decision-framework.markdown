Many companies are using multiple clouds today. The top reason I hear is companies don't want to be locked-in with just one cloud and be 100% dependent on it (what is resulting from that in the worst case?). As dangerous for the business as this scenario might look. Chances are high that being locked-in to one cloud might be the least important problem (provide justification, open source apis, other lock-ins). Gregor Hohope moving applications between clouds there are a number of more lock-ins to be considered. 

Hearing statements as "We go multi-cloud because we want to be as good prepared to be able to react to future market trands and events." I advise my customers to align their (multi)-cloud-strategy with their business strategy. 


What does that mean? 

Let's have a look at companies in the automotive sector, for example, because that is the industry sector I am most experienced with. Here, traditional companies like Mercedes and Volkswagen have a hard time to catch up and accelerate the way they deal with software-development. My gut feeling still is that these companies become much better every year in delivering high-quality software on cloud environments. However, some competitors in that space even increase the speed of acceleration faster than these traditional companies are able to which makes it alomost impossible to overtake them. 

![Velocity Diagram](velocity-diagram.png)

What does a (multi)-cloud-strategy look like that is aligned with such a business-strategy? It needs to free builders from undifferentiating tasks and let them focus on driving innovation for their core business. Undifferentiating tasks in the cloud space are running and maintaining database infrastructure, ordering and putting compute hardware into operation, maintaining a microservice runtime like Kubernetes on two clouds, just to name a few. 

Since this article is about multi-cloud strategy and its alignment with business-strategy I want to look on the last point of the last paragrah in more detail. Giving software-developers the option of choosing between cloud providers implies investing in setting up and maintaining two cloud environments that need to meet the security and compliance rules of a company. In addition both cloud providers should provide to a certain degree the same feature set to developers. A microservice runtime like Kubernetes might need to be maintained multiple times, or a VPN connection to a data center owned by the company needs to be set-up and maintained multiple times. All of this does not come for free. In addition giving the option to developers to choose freely between a set of clouds can make integration of applications more difficult from a technical as well as from a commercial side. 

Looking at the whole picture again, many companies are well advised to go all-in with one cloud provider as their first revision of their (multi)-cloud-strategy. The reduction in complexity gives developers the opportunity to focus on the core business driving innovation and speed-up software-development. Companies that focus on a possible future vendor-lock-in-problem are risking to miss out on new market opportunities their competitors are pursuing. Being prepared to be able to easily switch between cloud-providers cannot prevent a company of becoming irrelevant. 

Still, there are companies out there that focus on providing what seem to be the right tools for their developers to strife and create business-value. However, I propose to turn arond and focus on the developers themselfes. Your developers know best how to achieve their task having goals that are clear and measurable. Companies these days need to listen to their developers remove road-blocks, and give them the tools they need to solve their problems in the most elegant way instead of focusing on problems that might never materialize like the seemingly important ability to switch between cloud-vendors. 

I want to stress this argument of focusing on your developers and their productivity by giving an example before we conclude this article. Let's assume there is a company that is facing the situation of needing to switch cloud providers, for reasons of a price increase, for example. A Company that in the past focused on creating an integrated cloud platform on top of the established cloud-vendors now might feel that their up-front investment eventually pays off. Let's further assume that this company did not invest in the training and continious learning of their development teams. The skills these teams have do not fit to what is needed to be able to seemlessly implement the transition from one cloud provider to the other. In addition these development teams might not be able to adapt and learn who the other cloud provider works. 


multi cloud ansatz: bestimmte tools sind nur in bestimmten clouds verfügbar

As a conclusion, let's compare the approaches of both companies: The first company went all-in with one cloud provider, focusing on the development of hard- and soft-skills of it development force. With the abilities this companie now has, it is easier for the developers to learn the new cloud platform, plan, and take the decsions on how to move the applications from one cloud-provider to the other. The second company focused on creating a platform that integrates multiple-cloud providers not providing developers the ability to learn and improve their skills on the way. This company now faces the challenge of having a tool at hand that can help increase the speed to transition from one cloud-provider to the other. However, developes lack the skills needed to operate on the new cloud-platform. They also have a hard time to learn and adapt to this new environment. 


(companies focusing on increasing software delivery performance are also having a much better opportunity to react to unforseen events like the close down of a cloud provider )

not able to keep up => why invest in a problem that might be obsolete if a company gets irrelevant in the future




Moving applications between hosting environments is not considered to be a one-dimensional problem, but a multi-dimensional one. To start solving a multi-dimensional business-problem a strategy describing the way of solfing such problems is helpful. 

Only looking at potential cost savings running applications on a different runtime-environment like a public cloud or another cloud region can be such a strategy. However, using this strategy it should then also contain the risks associated with them. Such risks might be how the respective business relying on the applications to be moved is affected or whether certain compliance criteria are violated. 

Figure 1 shows Gregor Hohpes approach to architecture-decision making. 

![decision pipeline](../docs/assets/images/decision-pipeline.png)
*Figure 1: Decision Pipeline*

According to Gregor a decision should be based on a set of princoples derived from a company-strategy. The process of deriving decision-principles from a strategy might take considerable effort uncovering conflicting goals on the way, for example. However, doing this kind of work as early as possible is going to prevent pain associated with high cost for the business later on. 
Coming back to the business strategy of these traditional automotive companies. Such companies have a natural need to speed up their software-development processes be still be relevent in the near future which is grounded in their business strategy. 

---
layout: post
title:  "Is a Multi-Cloud Strategy Just About Using Multiple Clouds?"
date:   2024-03-05 10:24:47 +0200
categories: AWS, Multi-Region
---