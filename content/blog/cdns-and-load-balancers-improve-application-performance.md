---
path: /CDN
date: 2020-06-12T16:50:15.412Z
title: CDNs and load balancers improve application performance
description: A blog about the benefits of CDNs and load balancers
---
I recently deployed a serverless application on AWS. I used S3 to host the static pages, and CloudFront, a content delivery network (CDN), to ensure performance. Previously, I deployed another application to Elastic Beanstalk, where I encountered load balancers, again to maintain a functional application. I didn’t know the difference between CDNs and load balancers, but I knew both had important benefits.



A CDN refers to a geographically distributed group of servers, which work together to provide fast, reliable, cost-effective, and secure websites. A CDN places servers at exchange points between different networks. This geographical distribution enables CDNs to reduce costs, and transit times. A CDN allows for the quick transfer of assets needed to load internet content. CDNs cache content, which improves website performance. CDN benefits specifically include:

* Fast- geographical distribution reduces the distance between users and website resources enabling faster service. Instead of all users accessing one server, users access servers close to them.
* Reliable- load balancers distribute network traffic evenly across multiple servers, and redirects traffic from failed servers
* Secure- encryption promotes information security
* Cost-effective- caching reduces server data provisioning costs



A load balancer distributes network traffic across multiple working servers to maximize speed and capacity utilization. A load balancer provides the flexibility to add and subtract servers as demand dictates, and removes failing servers from the server pool. Load balancers use the following algorithm to distribute traffic:

* Sequential distribution of requests
* Requests sent to the server with the fewest active connections
* Requests sent to the server with the fewest active connections, and lowest average response times
* Requests distributed based on a predefined categorization such as associating certain IP addresses with servers



Both CDNs and load balancers protect against distributed denial of service (DDoS) attacks. This type of cyber-attack involves flooding a resource with superfluous requests in order to overload systems, and prevent legitimate requests from being fulfilled. CDNs and load balancers work together to ensure reliable, efficient, and secure applications.
