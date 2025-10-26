---
title: "Canary and Rolling Deployment Strategies Explained"
excerpt: "Learn the differences between canary and rolling deployment strategies, how they work, and when to use them in production environments."
date: 2025-02-22
tags:
  - devops
  - deployment
  - canary
  - rolling deployments
  - strategies
author: "Hamza Mateen"
draft: false
toc: true
layout: "blog"
readingTime: 6
slug: "canary-vs-rolling-deployments"
coverImage: "/images/deployments.png"
---


## What is a Deployment Strategy?

_Nothing beats the sensation of pushing your code to production._  
The feeling you get upon seeing your code going live, to be tried out by real users, is just mesmerizing. But, this feeling can be short-lived or may even remain unexperienced if the **deployment strategy** employed isn't the most suitable one for the project in question or a given scenario.

According to OpenShift:

> A deployment strategy is a way to change or upgrade an application with the aim of no perceptible downtime.

There are numerous deployment strategies employed in Software Development and DevOps but two of them very important and widely used are;

1.  Rolling Deployment
2.  Canary Deployment

## Rolling Deployment

In rolling deployment, the upgraded version of system or app is brought online in **incremental** changes.  
Different parts of the system are introduced to the production environment over time. It is a staged rollout approach where different components of the system are upgraded one at a time during each iteration of the deployment.

## Let's Imagine

We have 3 servers running our system or application currently deployed with version **v1.0**.  
In a rolling deployment, we might take down the first server, load it with the upgraded version **v2.0** of the application, and bring it back online.  
When we have made sure that everything works fine and as expected, we move on to deploy our latest version on the next server in our server line up; we take down the legacy server, load it up with latest upgrade and pull it back online. This process is repeated until every server is operating on the latest upgrade.  
In this way, the latest application is gradually deployed to the production environment.

The following illustration shows rollout phases that take place in this rolling deployment example.

![rolling](https://iq.opengenus.org/content/images/2023/09/rolling.png)

## Advantages

Rolling deployment has some key benefits, such as:

### Downtime Prevention

This strategy prevents downtime because even if one server is taken down in order for an upgrade, other servers are still operational and receive traffic accordingly. The load balancer of the app can be configured to distribute the traffic efficiently among the servers online. In this way, the users don't experience any service unavailability and upgrade process goes by smoothly.

### Early Mitigation

During rolling deployment, if our upgraded system shows any undefined behaviour or if any issues are spotted, they can be mitigated early on. Since the rate of _upgrade propagation_ is slow, the deployment can be put a halt to or hotfixes can be issued to remedy the discrepancies on the go.  
The gist is that we are only exposing a small part of our system to the new version at any given time.

## Disadvantages

Everything comes at a cost and Rolling Deployment is no different;

### Slow Deployment

One major pain point of this strategy is that application upgrade happens at a very slow rate. This can be an issue in case if currently deployed version is facing downtime or contains any bugs.

### Problem Propagation

If some problems slip through the initial checks and remain un-addressed, those can make it through the whole system bringing the entire application down or at the risk of failure.

### No Targeted Rollouts Support

Rolling deployment doesn't support _targeted rollouts_ because the user base is not controlled whether to be redirected to the newer version or current one. The load balancer randomly selects a server based on the current server load, following a random allocation method.

**Rolling deployment strategy balances the deployment impact of upgraded versions to production environment and mitigates the risk of potential downtime during an upgrade. So it is a common choice in Software Devlopment and DevOps to choose this strategy for app deployment.**

## Canary Deployment

In Canary Deployment, a subset of already running servers are deployed with the newer version of the system and a smaller set of users are systematically redirected to these servers. This set of servers is called a **Canary**.

This strategy is actually a derivative of an age-old practice by coal miners. They used to take _Canary birds_ into coal mines in order to find out if the toxic gases within the mine are reaching dangerous levels. In case they did, the bird would get distressed and signal the miners to evacuate.

## The Strategy

During canary deployment, the small set of servers are chosen based on some predefined criteria.

The canaries can be selected based on _geographical location_, _device type_ or just a specific group of _users_. It really depends on the need of hour, but once chosen, the canaries are used to monitor the performance of the new version under real-world conditions but on a much smaller and safer scale.

If the system performs well and as expected, then staged rollout of the newer version is performed to the remaining servers, gradually upgrading the whole system. But if there occur any problems, the deployment can be halted, thereby, affecting a much smaller userbase. The problems can be fixed later right inside the canary, then tested out and deployed again.

let's solidify our understanding through a practical use case scenario.

## Let's Imagine... Again

We have 6 severs running version **v1.0**.of our system and we want to use _Canary Deployment_ to upgrade our system. In this case, we need to define a _criteria_ to select the canary servers. Let's say we opt to use _33%_ of our servers to run the newer version. To do so, we take down 2 of 6 servers, transform them into a separate _cluster_, and load them up with the version **v2.0** of our app and redeploy them to the production line up.  
Now we have 2 separate deployments of the same system. We can direct a certain amount of traffic, ideally 33% in this case, and monitor the performance of the version **v2.0**.

The figure below, emulates the actual implementation of this scenario.

![canary](https://iq.opengenus.org/content/images/2023/09/canary.png)

## Advantages

Canary deployment comes with its own perks, for example;

### Resilience

Even if something goes wrong, we already have 67% of our servers operating on the stable version of our app so we can redirect the impacted users there. A slightly large load will develop on the existing server, but user frustration can be evaded in this way. Which means that our deployment strategy is resilient in case of any faults.

### Targeted Rollouts!

Perhaps the most important part of Canary deployment is that it enables us to perform _targeted rollouts_. We can customize our user selection criteria to our liking such as testing our system's performance on specific device type or user's geographical location.

## Disadvantages

This strategy also comes with some challenges, such as:

### Careful Monitoring & Testing

Canary deployment requires careful _monitoring_ in order to observe the system's performance. Automated _testing_ is required to perform system's performance evaluation

### Scaled Automation

If the system deployed to Canary works fine, its redeployment to the rest of servers requires extensive _automation_ so that newer version can be rolled out as soon as possible.

To control the deployment, extra _infrastructure_ tooling is required in order to configure the _allocation_ or _deallocation_ of resources in case if scaling up or halting the deployments is required, respectively.

**Canary deployment is often employed alongside the Rolling deployment strategy in order to bring best of the both worlds together.**
