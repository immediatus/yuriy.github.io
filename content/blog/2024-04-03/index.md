+++
authors = [ "Yuriy Polyulya" ]
title = "Routing Optimization"
description = "In this post, we will discuss the routing optimization problem and some of the algorithms used to solve it."
date = 2024-04-03

draft = true

[taxonomies]
tags = ["distributed-systems", "optimization"]

[extra]
toc = false
+++


&nbsp;&nbsp; Routing optimization is a common problem in distributed systems. It involves defining of optimization criteria like minimize waiting (customer satisfaction criteria) and same time maximize utilization (cost optimization criteria). There are many algorithms that can be used to solve this problem, each with its own strengths and weaknesses. In this post, we will discuss some of the most popular algorithms used to solve the routing optimization problem with a focus on distributed systems where [perfect information<sup>[1]</sup>](https://en.wikipedia.org/wiki/Perfect_information) is not guaranteed:

* Round Robin (or pure Random);

* Power of Two Choices;

* Weighted Random.

### Context for the Problem

&nbsp;&nbsp; Variety of the distributed systems is wide and routing problem can be different, and here would like to define set of properties for distributed system for which comparison will be done:

&nbsp;

**Unit of Work (UoW)** - the type of work that needs to be done, and it has next properties:

* **Heterogeneous** - different UoW can occupy only one slot on the server but for different duration;

* **Independent / Non-blocking** - UoW can be executed in parallel, and it can't be blocked by other UoW;

* **Non-preemptive** - UoW can't be interrupted;

* **Non-migratory** - UoW can't be moved to another server.

&nbsp;

**System** - the set of servers and routers with next properties:

* \\(S\\) - **Number of Servers** in the system:
  * All servers have the same number of **slots** - the number of UoW that can be executed in parallel on a server;

* \\(R\\) - **Number of Routers** in the system:
  * Router have no information about UoW - no perfect information. 
  * Router have partial information about the state of the servers - information is limited to the number of servers and their last know availability load (can be not actual).

&nbsp;

**Optimization Criteria** - the set of criteria that need to be optimized: 

* **Unit of Work execution performance** - this is proportion between effective time and total time of UoW execution.

  \\[P = \frac{\Delta t_{UoW}}{\Delta t_{UoW} + \Delta t_{waiting}} \\]

  _where:_ 
    * \\(\Delta t_{UoW}\\) - **execution duration** - the time a UoW occupies a slot on a server;
  
    * \\(\Delta t_{waiting}\\) - **waiting duration** - the time a UoW spends waiting in the queue before being executed.

&nbsp;

* **Server Utilization** - this is proportion between total time of UoW execution and total time of server availability.

  \\[U = \frac{\Delta t_{UoW}}{\Delta t_{UoW} + \Delta t_{idle}} \\]

  _where:_ 
    * \\(\Delta t_{idle}\\) - **Idle duration** - the time a server is not executing any UoW.

&nbsp;

* **Optimization Function** - the function that combines the criteria and defines the optimization goal.

  \\[F = \alpha \cdot P + \beta \cdot U \\]

  _where:_ 
    * \\(\alpha\\) - **performance weight** - the weight of the performance criterion in the optimization function;
  
    * \\(\beta\\) - **utilization weight** - the weight of the utilization criterion in the optimization function.

    *  \\(\alpha + \beta = 1\\). 


  **Note**: **performance weight** and **utilization weight** has a contradiction nature, since maximize utilization encourages queuing in the system what leads to minimize performance.

### Algorithms

#### Round Robin

&nbsp;&nbsp; The Round Robin algorithm is one of the simplest routing algorithms. It works by assigning each request to the next available server in a circular fashion. This ensures that all servers are utilized equally and that no server is overloaded. However, it does not take into account the load on each server, which can lead to some servers being underutilized while others are overloaded.

#### Power of Two Choices

&nbsp;&nbsp; The Power of Two Choices algorithm is based on the idea that choosing the best of two random choices is better than choosing the best of one random choice. It is an improvement on the Round Robin algorithm, and it works by randomly selecting two servers and assigning the request to the one with more preferable parameter. This can ensure that the optimal server is chosen more often, leading to better load balancing and utilization of the servers. 

#### Weighted Random

&nbsp;&nbsp; The Weighted Random algorithm is another improvement on the Round Robin algorithm. It works by assigning each request to a server based on a weighted random selection. The weight of each server can be determined by different criteria like server performance, server utilization, or combination of them. This ensures that the load is distributed more evenly with respect to the optimization function.


### Conclusion

&nbsp;&nbsp; Each of these algorithms has its own strengths and weaknesses, and the best algorithm to use will depend on the specific requirements of the distributed system. And best way to choose the right algorithm is to simulate the system and evaluate the performance and utilization of the system with respect to the optimization function.

## Simulation

&nbsp;&nbsp; Simulator code [routing-simulator](https://github.com/immediatus/routing-simulator) is available on GitHub. Simulation will be done for the system with parameters defined above.

#### Configuration #1:

| Parameter | Value                                        |
| :-------- |:---------------------------------------------|
| Number of Servers | 10                                           |
| Number of Routers | 1                                            |
| Slots per Server | 5                                            |
| Unit of Work | 1 slot for [1 ... 5] time units (normal dist)|
| Performance weight | 0.5                                          |
| Utilization weight | 0.5                                          |
