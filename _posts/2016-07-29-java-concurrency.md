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

#### Models of concurrency

1. Parallel worker --> One delegator assigns work to multiple worker threads, who each run the job from beginning to end. Java application servers are designed in a somewhat similar fashion.<br>
  Advantages:<br>Easy to understand an implement. To increase parallelism add more workers (threads)
  Disadvantages:<br>Multiple threads accessing the same shared data; trying to modify the same shared space can lead to data inconsistency.<br>Due to high contention , some parts are executed serially which defeats the purpose of concurrency.  <br>Persistent Data structures maintain a previous version of the data , but managability soon becomes an issue. <br>Worker threads are stateless i.e they process the task from the beginning every time. <br>Job Ordering is non deterministic.
2. Assembly Line / event driven systems / Reactive systems --> One delegator assigns work to a worker. The output of worker1 is assigned to worker2 . In case of IO call, the control is relinquished and once the IO operation is over, the next worker picks it up.
3. Actor Models & Pub sub models --> Each worker thread is an actor in actor model and communicate with each other by sending messages to each other's inbox. In Pub sub model the worker threads send messages to channels and other worker threads listen on these channels and pick up the jobs. 
  Advantages:<br>No shared state between workers. <br>Worker threads can be stateful.<br>Mechanical sympathy / Hardware Conformity is better because we are esentially writing programs as though they are all single threaded applications .<br>Job ordering is possible .
  Disadvantages:<br>Work execution is pread across multiple workers / threads.<br>Callback handlers for all workers needs to be written well.<br>Extensive callback handlers can make code messy.
4. Functional Parallelism --> Programs are implemeneted as functional calls and for each call to a function, the values are copied in order to avoid race conditions with the shared data.(i.e each function call is an atomic operation)
5. Same threading --> single threaded systems are scaled out to N single threaded systems running parallely. 
  Advantages:<br>No shared states.<br>Able to utilize all the cores on the CPU. example (Microservices).<br>Thread communication takes place via pipes, queues, TCP sockets, message passing etc.<br>For processing: the data is first copied so that the data cannot be changed while another thread i working on it.<br>Concurrency problems can be avoided and no need to use concurrent data structures.

#### Concurrency vs parallelism 

1. Concurrency is executing and progressing of group of tasks at the same time. If only one CPU is present and only a single core . it can still do multiple tasks .. but by time slicing.<br>
2. Parallelism is division of a super task into multiple smaller tasks and executing these sub tasks in parallel. If division of task does not happen , then it is probably concurrent execution and not parallel.<br>

#### 2 ways to create thread(Old Java)

Implements Runnable and extends Thread. 

#### Critical sections and Race Conditions

A section of code that is executed by multiple threads and if the order of execution of threads affects the result of the section, then it can be called as critical section. e.g incrementing a class level variable without synchronization. 2 threads can access the same increment method at the same time each thread perfroming its own incrment operation(increment with different numbers for both threads) can lead to data corruption. 

Both threads are accessing the same resource and races through the critical section leading to data inconsistency. 
Solution to avoid race condition is to make the smalled blocks of code as synchronized so that only one thread can access the variable at a one point of time . 

#### Resources shared by Java Threads. 

Local variables are stored in the executing thread's local stack . so it cannot be accessed by other threads. all local primitive variables are inherently threadsafe. However only the object references are saved on the thread stack. the objects itself are saved in the shared heap . If the object is not made avaialble to other threads then the object is thread safe. 
Object member variables are stored in the heap along with the other Objects. the same instance should not be shared between 2 threads , else it will lead to a race condition.However, if a 'new' operator is used, then they are different instances and hence it will not lead to a race condition.   
Thread control escape Rule : If a resource is created, used and disposed within the control of the same thread, and never escapes the control of this thread,the use of that resource is thread safe.
By creating wrapper classes around Immutable classes we are making them devoid of being thread safe. In such cases ensure the operations on the immutable classes are synchronized. 

#### Java Memory Model

1. The Java memory model specifies how the JVM interacts with the computer's memory i.e RAM.
2. The memory is essentially divided into thread stacks and Heap Memory.
3. All Local variable are saved in the thead stack . All Object References are local to the thread stack.The objects itself are saved in the heap memory. Hence even Integer / Double variables which are objects are saved in the heap. 
<br>e.g<br>
```
//t1 stack --> methodOne () --> LocalVariable1, t1.localVariable2 (only reference); t1.methodTwo()-->t1.localVariable1 (only reference)
//t2 stack --> methodOne () --> LocalVariable1, t2.localVariable2 (only reference); t2.methodTwo()-->t2.localVariable1 (only reference)
//heap --> MysharedInstance,localVariable2 object (shared), t1.localVariable1 and t2.localVariable1 are creating 'new Integer' 
hence 2 objects on heap.

public class Main{

  psvm {
    new Thread(new MyRunnable()).start(); //assume name t1
    new Thread(new MyRunnable()).start(); //assume name t2
  }
}


public class MyRunnable implements Runnable() {

    public void run() {
        methodOne();
    }

    public void methodOne() {
        int localVariable1 = 45;

        MySharedObject localVariable2 =
            MySharedObject.sharedInstance;

        //... do more with local variables.

        methodTwo();
    }

    public void methodTwo() {
        Integer localVariable1 = new Integer(99);

        //... do more with local variable.
    }
}

public class MySharedObject {

    //static variable pointing to instance of MySharedObject

    public static final MySharedObject sharedInstance =
        new MySharedObject();


    //member variables pointing to two objects on the heap

    public Integer object2 = new Integer(22);
    public Integer object4 = new Integer(44);

    public long member1 = 12345;
    public long member1 = 67890;
} 
```
#### Hardware memory architecture

CPU Registers --> cpu cache--> RAM(main memory). 
shared objects are stored in the main memory. If 2 threads are accessing the same shared object, It may be loaded into 2 different CPU caches. Each thread may then modify the value in the CPU register and save it back in the main memory causing a data inconsistency. To avoid this , if we use volatile keyword, the variable is read directly from the main memory and written back . Similarly synchronized will ensure that the critical code section will be executed by only one thread and the variables tat are modified are flushed back into the main memory.

#### Volatile Variables
Volatile variables are read and written directly from the main memory. However, there is no thread locking while performing the read and write operation. A few things to keep in mind are: when there is a read or write operation on a volatile variable, all the other corresponding variables are also flushed into the memory.(called as a happens before paradigm for volatile variables). This ensures that when another thread tries to view a volatile variable it gets visibility into other shared variables that has been updated by other threads as well. However, since the threads are not blocked, synchronized is essential to ensure the same view of the object to all threads. Accessing volatile variables also prevent instruction reordering which is a normal performance enhancement technique. Thus, you should only use volatile variables when you really need to enforce visibility of variables. 

#### ThreadLocal Variables
Any object declared as a ThreadLocal will be seen only by the threadOwner. The declaration looks as follows 
```   private ThreadLocal<Integer> threadLocal = new ThreadLocal<Integer>();```


#### TO READ
  New, asynchronous "shared-nothing" platforms and APIs like Vert.x and Play / Akka, LMax Disrupter, Qbit , Fork and Join framework in Java 7, and the collection streams API in Java 8. 
