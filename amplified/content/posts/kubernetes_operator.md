
+++
date = "2019-09-06"
title = "Kubernetes Operators"
slug = ""
tags = [
    "Kubernetes",
    "tools",
    "Cloud"
]
categories = []
series = []
+++

## How to explain Kubernetes Operators in plain English.

### Refer https://operatorhub.io/ (For Range of Operators)

What are Kubernetes Operators, and why are they so valuable to organizations working with containers? 

Why is Kubernetes such a hot topic in IT?

To properly answer that question, you need to look at the advent of containers and microservices, which brought wonderful new capabilities – and new challenges – to software development and infrastructure.

“Containers are fantastic things: They allow for an even greater leap in resource efficiency over virtual machines than VMs claimed over hardware servers,” says Jeremy Thompson, CTO at Solodev.

That leap doesn’t come free of charge, however.

“The mandate that a container focus on a single service – your web app, a caching layer, the database, a logging system – means that a modern app can beget a small army of containers,” Thompson explains.

That “small” army might actually mean a massive number of containers that, Thompson notes, must communicate with one another, handle failures, and scale independently based on ever-changing needs. Managed in a manual fashion, these operational needs can overtax even the sharpest DevOps teams.

Enter container orchestration – and Kubernetes as the leader of the pack, in part because of its DNA: Kubernetes was originally an internal Google system used to manage billions of production containers a week. Today, Kubernetes is one of the liveliest open source projects around, another key reason for its popularity. (See also Kubernetes by the numbers: 10 compelling stats.)

“As an orchestration framework, Kubernetes eases the burden of configuring, deploying, managing, and monitoring even the largest-scale containerized applications,” Thompson says.



## The problem that prompted Kubernetes Operators 

That’s not where the story ends, however. The conventional wisdom of Kubernetes’ earlier days was that it was very good at managing stateless apps. But for stateful applications such as databases, it wasn’t such an open-and-shut case: These apps, as Thompson puts it, required more hand-holding.

“Adding or removing instances may require preparation and/or post-provisioning steps – for instance, changes to its internal configuration, communication with a clustering mechanism, interaction with external systems like DNS, and so forth,” Thompson explains. “Historically, this often required manual intervention, increasing the DevOps burden and increasing the likelihood of error. Perhaps most importantly, it obviates one of Kubernetes’ main selling points: automation.”

That’s a big problem. Fortunately, the solution emerged back in 2016, when coreOS introduced Operators to extend Kubernetes’ capabilities to stateful applications. (Red Hat acquired coreOS in January 2018, expanding the capabilities of the OpenShift container platform.) Operators became even more powerful with the launch of the Operator Framework for building and managing Kubernetes native applications (Operators by another name) in March 2018. Amidst the broader excitement about Kubernetes, the importance of Operators seems understated – when in fact it would be hard to overstate their importance.

So we’re here with a brief primer for IT leaders on Operators: What are Kubernetes Operators, and why are they so important? We asked a range of experts to help CIOs and their leadership teams get up to speed. Let’s start by getting grounded in some brief definitions.

## What is a Kubernetes Operator?

“An operator is a way of building an application and driving an application on top of Kubernetes, behind Kubernetes APIs. The idea is that when you have an application, like a database like Postgres or Cassandra…any complex application needs a lot of domain-specific knowledge,” Sebastien Pahl, director of engineering, Red Hat OpenShift, explained in a Kubecon 2018 presentation. You can use Kubernetes tools to try and solve some of those problems, he notes.

“But in the end, when it comes to really fully automating an application, and by fully automating, I mean handling updates from one version to another without waking people up, handling failure recovery if it’s needed, scaling the application up and down depending on some scenarios – all of that should be handled automatically,” Pahl says. “Humans should not be involved in this kind of operation because it kind of breaks the promise that containers gave us. Containers’ promise was ‘Hey, you package it once, it runs everywhere.’ Well, that’s true for developers, but in production, there’s so much more that needs to be done.” (Watch Pahl's full talk on Kubernetes Operators in Depth.)

Here are some other definitions of Kubernetes Operators that you can use:

“A Kubernetes Operator helps extend the types of applications that can run on Kubernetes by allowing developers to provide additional knowledge to applications that need to maintain state.” –Jonathan S. Katz, director of customer success & communications at Crunchy Data

“A Kubernetes Operator is an abstraction for deploying non-trivial applications on Kubernetes. It wraps the logic for deploying and operating an application using Kubernetes constructs. As an example, the etcd operator provides an etcd cluster as a first-class object. Gone are the days of deploying an etcd cluster using a complicated collection of stateful sets, crds, services, and init containers to manage bootstrapping and lifecycle management, et cetera.” –Richard Laub, staff cloud engineer at Nebulaworks

“Operators are software written to encapsulate all of those operational considerations for a specific Kubernetes application and ensure that all aspects of its lifecycle, from configuration and deployment to upgrades, monitoring, and failure-handling, are integrated right into the Kubernetes framework and invoked when needed. The Operator Framework provides an SDK to handle calling the Kubernetes API, making it easier to focus on your own business logic. If it makes sense for your app, you can extend the Kubernetes API by adding domain-specific resource types – for example, ‘backup’ – which can then be used via the standard Kubernetes tooling such as kubectl.” –Jeremy Thompson, CTO at Solodev

## Why are Kubernetes Operators so important?
Definitions are foundational knowledge, but a really useful high-level understanding of Operators requires connecting the “what” to the “why.” Why are Operators valuable? We break their value down into five overlapping categories, with additional insight and context from our experts:

1. Operators extend Kubernetes functionality
This is really the table-stakes significance of Operators: They extend the power of Kubernetes, especially to stateful apps, in a far more manageable and accessible fashion than before. As the coreOS announcement introducing the original etcd Operator and Prometheus Operator said, “stateless is easy, stateful is hard.” Operators bring the balance back toward easy for a wider range of applications.

    “Kubernetes Operators allow developers to easily extend Kubernetes functionality for specific software [and] use cases,” says Ben Bromhead, CTO at Instaclustr. (Bromhead led the development of the Kubernetes operator for Apache Cassandra.) “A great example of this is database operators, which allow Kubernetes users to safely deploy and manage certain databases without needing to build their own workarounds.”

2. Operators systematize human knowledge as code
If you had to sum up Kubernetes in a word, the best choice might not be “orchestration” but “automation.” That’s really what it’s all about: Kubernetes enables the automation of the infrastructure (and corresponding operational burden of managing that infrastructure) necessary for running containerized applications – a must when running these apps at scale in production environments.

    Essentially, automation in an IT context means translating human knowledge and effort – often painful effort – into software.


3. Operators do this in a scalable, repeatable, standardized fashion
The value of automation is inextricably linked to its scalability and repeatability – if you need to “re-automate” a process every time it’s needed elsewhere, that’s not going to cut it.

    The value begins cascading when you put this into a production context.

    “The Operator really showcases its power when applying these commands en masse. For instance, with PostgreSQL, there are a variety of steps you need to take in order to provision a replica copy of a database. Imagine you have to do this hundreds, if not thousands, of times,” Katz says. “An Operator allows you to perform these complex administrative tasks easily and in a standardized way.”



4. Operators ultimately improve resiliency while reducing the burden on IT teams
Operators ultimately help pay off on the promise of containers (and container orchestration) without running your teams into the ground. Simply put, they reduce complexity.

From another vantage point, consider life without Operators.

    “Without Operators, many applications need intervention to deploy, scale, reconfigure, upgrade, or recover from faults,” Thompson says. “If your app – or apps that you depend on, such as your database management system – [requires] DevOps engineers hovering over a keyboard in these critical moments, hoping they get the steps correctly, you’re almost certain to have greater downtime and more stress in your team.”

5. Operators prove particularly useful in multi-cloud and hybrid cloud environments
The importance of Operators is underlined by the increasingly distributed nature of multi-cloud and hybrid cloud environments. Operators allow, for example, a developer to do what she does best without necessarily needing domain expertise in a particular infrastructure environment.

    “Operators allow developers to create some immensely powerful tools that can be used on any infrastructure where Kubernetes is installed,” says Leo Shemesh, CTO at Jackpocket. "As opposed to using one-off installations on a specific application, an Operator can be used to provision multiple applications in a consistent manner while adhering to best practices for that particular installation.