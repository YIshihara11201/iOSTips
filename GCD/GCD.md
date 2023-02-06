# Grand Central Dispatch
This article is a key-points note for  

"Pro Multithreading and Memory Management for iOS and OS X: with ARC, Grand Central Dispatch, and Blocks,   
by Kazuki Sakamoto, Tomohiko Furumoto, Apress, April 25 2012."  

<br>

## What is GCD?
GCD is a technology to make multithreading programming elegant and simple.  
Before GCD multithreading was achieved with `performSelector` methods or `NSThread` class.

## Multithreaded Programming

* ### Single thread 
  One CPU can execute one instruction at the same moment.  
  The execution never splits to run two instructions concurrently.  
  A path of CPU execution is equivalent to a thread.  

* ### Multi thread
  Multithread means that an application has many paths for one CPU.  

## Context Switch
The XNU kernel that is a core part of OS X and iOS switches between the paths at regular time intervals and when it get an OS event, such as system calls, an executing state for a path, including CPU registers, is stored in one of the memory blocks assigned to each path.   
And the other executing state is copied back from a memory block to the CPU registers.  
By this way, one CPU can virtually execute multiple threads.  

## Disadvantages of Multithreaded Programming
1. Data Race
2. Dead Lock
3. Too much threads consuming memory  
<br>

![multithreaded_problems](https://user-images.githubusercontent.com/98417271/216885982-b05629b6-2329-4f3e-a9e7-c5f15b952e85.png)

<br>

## Why multithreaded programming?  
A) To make the application highly responsive.  

In an application, a thread is started just after the application is launched.  
The thread draws user interfaces, handles events from a touch panel, and so on. This thread is called the main thread.  
If the main thread executes a time-consuming task, such as image processing for AR or database access, the task blocks all the other tasks on the main thread. On OSX and iOS, the main loop, called RunLoop on the main thread, should not be blocked because only the main thread can update user interfaces. 
When it is blocked, user interfaces aren’t updated and the same still image is displayed for quite a long time. To avoid such a situation, time-consuming tasks should be executed on the other threads.  
<br>

![why_multithreaded](https://user-images.githubusercontent.com/98417271/216886212-bd9d7e40-5619-4abc-be9c-e5ebf9c61cde.png)

<br>

## Dispatch Queues
Fundamental component consisting of GCD. 
A queue to store tasks to be executed.  
There are two kinds of dispatch queues. One is a serial dispatch queue, which waits until the current running task finishes before starting another task. The other is a concurrent dispatch queue, which doesn’t wait. 

* ### Serial dispatch queue. 
  Only one task runs at a time, which means that the result is always the same.  
  <br>
![serialQueue](https://user-images.githubusercontent.com/98417271/216889921-241ba1dd-3a17-462e-bb48-aa5c12822e16.png)  

* ### Concurrent dispatch queue. 
  Doesn’t wait until the tasks are finished and multiple tasks run concurrently.  
  The number of tasks running concurrently depends on the status of the current system; that is, iOS or OS X decides the number based on the current system status, such as how many tasks are in the dispatch queue, the number of CPU cores, or the CPU usage level.  
  <br>
![concurrentQueue](https://user-images.githubusercontent.com/98417271/216889951-a4ecd0a1-aaaf-48db-acd5-bdc0a585ec61.png)  

  The XNU kernel, which is a core part of iOS and OS X, decides the number of threads. It also creates threads to execute the tasks. When a task is finished and the number of running tasks is decreased, the XNU kernel terminates an unneeded thread. Just by using a concurrent dispatch queue, the XNU kernel manages multiple threads perfectly to run tasks concurrently. 

  Note that you should use a serial dispatch queue to avoid inconsistent data (race condition) that occurs because multiple threads update the same data, explained as one of the problems of multithreaded programming.  
  <br>
  ![serial_resolve_race_conditions](https://user-images.githubusercontent.com/98417271/216890161-910f3cc0-b3ba-49fc-8e90-c54f8b6c7c19.png)
  <br>

## APIs

* ### Dispatch queue creation
  *create serial dispatch queue*  
  ```objc
  dispatch_queue_t mySerialDispatchQueue = dispatch_queue_create("com.example.gcd.MySerialDispatchQueue", NULL);
  ```

  *create concurrent dispatch queue*  
  ```objc
  dispatch_queue_t myConcurrentDispatchQueue = dispatch_queue_create( "com.example.gcd.MyConcurrentDispatchQueue", DISPATCH_QUEUE_CONCURRENT);
  ```

* ### Task addition
  *add task asynchronously*   
  ```objc
  dispatch_async(myConcurrentDispatchQueue, ^{NSLog(@"block on myConcurrentDispatchQueue");});
  ```
  Calls to this function always return immediately after the block is submitted and never wait for the block to be invoked. The target queue determines whether the block is invoked serially or concurrently with respect to other blocks submitted to that same queue. Independent serial queues are processed concurrently with respect to each other.

* ### Retain conunt management
  *retain queue*  
  ```objc
  dispatch_release(mySerialDispatchQueue);
  ```
  *release queue*  
  ```objc
  dispatch_release(mySerialDispatchQueue);
  ```

* ### Main/Global dispatch queues (queues predefined by the system)
  *get main dispatch queue*  
  ```objc
  dispatch_queue_t mainDispatchQueue = dispatch_get_main_queue();
  ```
  *get global dispatch queue (with priority)*  
  ```objc
  dispatch_queue_t globalDispatchQueueHigh = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0);
  ```
    <br>
  Main dispatch queue is a queue to execute tasks on the main thread. As there exists only one main thread, the main dispatch queue is a serial dispatch queue. You should use it for tasks that must be done on the main thread such as updating the user interface, and the like.  
  
  Global Queues are concurrent dispatch queues that are usable anywhere in the application. If you don’t have any specific reason, you don’t need to create a concurrent dispatch queue by the dispatch_queue_create function in most cases. You can just obtain a global dispatch queue and use it. 

<br>

## APIs (Controlling Dispatch Queues)

* ### Dispatch group
  start a task to finalize something after all the tasks in the dispatch queues are finished. 
  ```objc
  dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
  dispatch_group_t group = dispatch_group_create();
  dispatch_group_async(group, queue, ^{NSLog(@"blk0");}); 
  dispatch_group_async(group, queue, ^{NSLog(@"blk1");}); 
  dispatch_group_async(group, queue, ^{NSLog(@"blk2");});
  dispatch_group_notify(group, dispatch_get_main_queue(), ^{NSLog(@"done");});
  dispatch_release(group);
  ```

* ### dispatch_sync
  It adds the Block to the dispatch queue synchronously.  
  The dispatch_sync function waits for the added Block to be finished.  
  Note that this method can lead to dead lock conditions.
  ```objc
  dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
  dispatch_sync(queue, ^{/* a task */});
  ```

  *dead lock example*  
  ```objc
  dispatch_queue_t queue = dispatch_get_main_queue(); dispatch_async(queue, ^{
  dispatch_sync(queue, ^{NSLog(@"Hello?");}); });
  ```

* ### dispatch_barrier_async
  The dispatch_barrier_async is a function for waiting for other tasks in a queue.  
  This can be used to avoid data race conditions.  
  This function is used with a concurrent dispatch queue created by the dispatch_queue_create function.  
  <br>
  ![dispatch_async_barrier](https://user-images.githubusercontent.com/98417271/216890203-f0ec696d-c312-4ae3-8ccc-bd13c8b8ce98.png)


  ```objc
  dispatch_queue_t queue = dispatch_queue_create( "com.example.gcd.ForBarrier", DISPATCH_QUEUE_CONCURRENT);

  dispatch_async(queue, blk0_for_reading); 
  dispatch_async(queue, blk1_for_reading); 
  dispatch_async(queue, blk2_for_reading); 
  dispatch_async(queue, blk3_for_reading); 
  dispatch_barrier_async(queue, blk_for_writing); 
  dispatch_async(queue, blk4_for_reading);
  dispatch_async(queue, blk5_for_reading); 
  dispatch_async(queue, blk6_for_reading); 
  dispatch_async(queue, blk7_for_reading);
  ```
