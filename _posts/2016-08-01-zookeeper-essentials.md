---
layout: post
title: "Zookeeper Essentials (Notes)"
date: 2016-08-01
---

A distributed system is composed of several components which communicate with each other over the network . e.g
Online Multiplayer games, gmail, yahoo mail etc. key characteristics to look out in a distributed system are : 
<pre>1. Resource sharing </pre><pre>2. Extendibility </pre><pre>3. Concurrency </pre><pre>4. Performance and scalability </pre>
<pre>5. Fault tolerance</pre><pre>6. Abstraction through API's </pre>

[Fallacies Of Distributed Computing](https://en.wikipedia.org/wiki/Fallacies_of_distributed_computing)

#### Challenges in coordinating distributed systems.

1. If master node saves the cluster configuration for all worker nodes , it can lead to a single point of failure . Even if that
is handled, an new addition of client machines , must automatically discover its peers and also register with the master (or master 
should discover it automatically).(Also called service discovery) Designing such a system is complex. 
2. Propogating configuration changes in the master to all worker nodes dynamically has to be designed carefully. 

#### About zookeeper
Its a centralized coordination server, distributed across a cluster of servers. termed as zookeeper ensemble.
zookeeper service provides distributed consensus, leader election, presence protocols , group management out of the box. It is 
implemented in java

