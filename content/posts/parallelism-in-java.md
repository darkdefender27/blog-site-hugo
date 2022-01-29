---
title: "Using CompletableFuture for parallelism, and variants of ThreadPoolExecutors"
date: 2022-01-29T18:40:02+05:30
draft: false
toc: false
images:
tags: 
  - java
  - parallelism
---


## Completable Futures & ForkJoinPool

CompletableFutures prove useful in areas where one has to perform multiple tasks in a parallel manner, probably wait on them and combine the results.

By default, the CompletableFuture implementation makes use of the commonPool provided by the ForkJoinPool framework. The distinguishing thing between other ThreadPoolExecutors & ForkJoinPool is the workStealing implementation.

Quoting the docs -
> all threads in the pool attempt to find and execute tasks submitted to the pool and/or created by other active tasks (eventually blocking waiting for work if none exist). This enables efficient processing when most tasks spawn other subtasks (as do most ForkJoinTasks), as well as when many small tasks are submitted to the pool from external clients. Using the common pool normally reduces resource usage (its threads are slowly reclaimed during periods of non-use, and reinstated upon subsequent use).

The pool is preferable for a use case that is non-blocking in nature.

Quoting the docs again -
> a ForkJoinPool may be constructed with a given target parallelism level; by default, equal to the number of available processors. The pool attempts to maintain enough active (or available) threads by dynamically adding, suspending, or resuming internal worker threads, even if some tasks are stalled waiting to join others. However, no such adjustments are guaranteed in the face of blocked I/O or other unmanaged synchronization.

If there happens to be a blocking call, it’ll block the execution of the other parts of the system that depend on the same commonPool. Hence, in general, it’s better to have a separate executor/thread pool passed to our CompletableFuture implementation if the requirement involves any form of blocking I/O.

This [blog](https://dzone.com/articles/be-aware-of-forkjoinpoolcommonpool) & [the official documentation](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/ForkJoinPool.html) further shares the common pitfalls of using the commonPool.

## Using a Custom ThreadPoolExecutor

ThreadPoolExecutors provide a means to execute tasks using the pooled threads. This not only reduces the overhead of creating new threads for each new submitted task, but also ensures that the resource utilization is not unbounded.

Instantiating the ThreadPoolExecutor requires setting the parameters appropriately; one should study the parameters such as core and maximum pool sizes, keepAliveTimes, workQueues & tune them to their use case. Refer the [JavaDocs - ThreadPoolExecutor](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/ThreadPoolExecutor.html) for understanding the meanings & the defaults of the parameters. That said, it’s urged to use the factory methods provided by the Executors class, a couple of them important ones are discussed below -

* [newWorkStealingPool()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/Executors.html#newWorkStealingPool()) - Creates a work-stealing thread pool using the number of available processors as its target parallelism level. It may use multiple queues to reduce contention. It however, does not guarantee the order of execution in which the requests were submitted.

* [newCachedThreadPool()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/Executors.html#newCachedThreadPool()) - Creates a thread pool that creates new threads as needed, but will reuse previously constructed threads when they are available, and uses the provided ThreadFactory to create new threads when needed. It uses SynchronousQueue (a good default choice for a work queue) with ​​unbounded maximumCorePoolSize. (ref: [Direct Handoffs strategy of Queueing](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/ThreadPoolExecutor.html))

Besides the above two, one can create a [newFixedThreadPool()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/Executors.html#newFixedThreadPool()) (reuses a fixed number of threads operating on an unbounded shared workQueue), [newSingleThreadExecutor()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/Executors.html#newSingleThreadExecutor()) (single thread operating on an unbounded shared workQueue), [newScheduledThreadPool()](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/Executors.html#newScheduledThreadPool()) (can be used to schedule tasks to run after a delay).

:bulb: As a matter of fact, gRPC, internally uses a CachedThreadPool by default for managing the app threads; It is in fact the default Thread Executor, reasons for the same are explained in the comment - https://github.com/grpc/grpc-java/issues/7381#issuecomment-684938279.

## References

* https://www.infoq.com/articles/Functional-Style-Callbacks-Using-CompletableFuture/
* https://dzone.com/articles/be-aware-of-forkjoinpoolcommonpool
* https://dzone.com/articles/how-much-memory-does-a-java-thread-take
* https://docs.oracle.com/javase/tutorial/essential/concurrency/pools.html
* https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/ThreadPoolExecutor.html
* https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/ForkJoinPool.html
* https://www.baeldung.com/java-completablefuture
* https://www.baeldung.com/java-fork-join#2-forkjoinpool-instantiation
