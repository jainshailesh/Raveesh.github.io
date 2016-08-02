---
layout: post
title: "Java Concurrency (Notes)"
date: 2016-07-29
---
## Introduction
1. Writing correct programs is hard; writing correct concurrent programs is harder.
2. Concurrent programs are more responsive and provide better resource utilization. 
3. A concurrency model explains how various threads in a system talk to each other in order to complete a given task.
4. In concurrent systems different threads talk to each other to complete a task. In distributed systems, different processes communicate with each other to complete a job 

### Models of concurrency
1. Parallel worker --> One delegator assigns work to multiple worker threads, who each run the job from beginning to end. Java application servers are designed in a somewhat similar fashion.
  <pre>
    1. Advantages: Easy to understand an implement. To increase parallelism add more workers (threads)
  </pre>
  <pre>
    2. Disadvantages: Multiple threads accessing the same shared data; trying to modify the same shared space can lead to data     inconsistency. <br> Due to high contention , some parts are executed serially which defeats the purpose of concurrency.     <br>Persistent Data structures maintain a previous version of the data , but managability soon becomes an issue. 
       <br>Worker threads are stateless i.e they process the task from the beginning every time. 
       <br>Job Ordering is non deterministic
  </pre>

2. Assembly Line / event driven systems / Reactive systems --> One delegator assigns work to a worker. The rest of worker1 is assigned to worker2 . In case of IO call, the control is relinquished and once the IO operation is over, the next worker picks it up.
3. Actor Models & Pub sub models --> Each worker thread is an actor in actor model and communicate with each other by sending messages to each other's inbox. In Pub sub model the worker threads send messages to channels and other worker threads listen on these channels and pick up the jobs. 
  1. Advantages: No shared state between workers. Worker threads can be stateful.Mechanical sympathy / Hardware Conformity is better because we are esentially writing programs as though they are all single threaded applications . Job ordering is possible . 
  2. Disadvantages: Work execution is pread across multiple workers / threads. Callback handlers for all workers needs to be written well. Extensive callback handlers can make code messy. 
4. Functional Parallelism --> Programs are implmeneted as functional calls and for each call to a function, the values are copied in order to avoid race conditions with the shared data.(i.e each function call is an atomic operation)
5. Same threading --> single threaded systems are scaled out to N single threaded systems running parallely. 
  1. Advantages: No shared states. Able to utilize all the cores on the CPU. example (Microservices). Thread communication takes place via pipes, queues, TCP sockets, message passing etc. For processing: the data is first copied so that the data cannot be changed while another thread i working on it. Concurrency problems can be avaoided and no need to use concurrent data structures. 

#### Concurrency vs parallelism 
1. concurrency is executing and progressing of group of tasks at the same time. If only one CPU is present and only a single core . it can still do multiple tasks .. but by time slicing. 
2. Parallelism is division of a super task into multiple smaller tasks and executing these sub tasks in parallel. If division of task does not happen , then it is probably concurrent execution and not parallel.

#### 2 ways to create thread(Old Java)
Implements Runnable and extends Thread. 

#### Critical sections and Race Conditions
a section of code that is executed by multiple threads and if the order of execution of threads affects teh result of the section, then it can be called as critical section. e.g incrementing a class level variable without synchronization. 2 threads can access the same increment method at the same time each thread perfroming its own incrment operation(increment with different numbers for both threads) can lead to data corruption. 

Both threads are accessing the same resource and races through the critical section leading to data inconsistency. 
Solution to avoid race condition is to make the smalled blocks of code as synchronized so that only one thread can access the variable at a one point of time . 

#### Resources shared by Java Threads. 
local variables are stored in the executing thread's local stack . so it cannot be accessed by other threads. all local primitive variables are inherently threadsafe. However only the object references are saved on the thread stack. the objects itself are saved in the shared heap . If the object is not made avaialble to other threads then the object is thread safe. 
Object member variables are stored in the heap along with the other Objects. the same instance should not be shared between 2 threads , else it will lead to a race condition.However, if a 'new' operator is used, then they are different instances and hence it will not lead to a race condition.   
Thread control escape Rule : If a resource is created, used and disposed within the control of the same thread, and never escapes the control of this thread,the use of that resource is thread safe.
By creating wrapper classes around Immutable classes we are making them devoid of being thread safe. In such cases ensure the operations on the immutable classes are synchronized. 




## TO READ
  New, asynchronous "shared-nothing" platforms and APIs like Vert.x and Play / Akka, LMax Disrupter, Qbit , Fork and Join framework in Java 7, and the collection streams API in Java 8. 
