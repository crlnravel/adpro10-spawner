Name    : Carleano Ravelza Wongso

NPM     : 2306213022

# 1.2. Understanding how it works.
![alt text](images/image.png)

When the program runs, we observe that hey hey! is printed before howdy!, even though the code for howdy! appears first. This happens because the function responsible for printing howdy! is executed asynchronously. When the async task is spawned, it gets scheduled but doesn’t run immediately, allowing the main thread to continue and print hey hey! right away. The async task is later picked up by the executor, which first prints howdy!, waits for two seconds using a timer, and then prints done!. This sequence highlights the non-blocking nature of Rust’s async/await system, where asynchronous tasks run concurrently with the main thread without halting its progress.

# 1.3 Multiple Spawn and Removing Drop

## What does spawning do?  
In this context, spawning sets up an asynchronous task (a future) to be run later by the executor. When a future is spawned, it gets wrapped in a `Task` struct that includes the future itself and a channel sender to re-schedule it. The spawner then sends this task to the executor's queue. This design enables the executor to poll and advance the future toward completion over time. This separation between spawning and execution allows for efficient management of multiple async tasks.

## What are the roles of the spawner, executor, and drop?  
The **spawner** is in charge of creating and scheduling asynchronous tasks. The **executor** takes care of executing those tasks by polling them and progressing them toward completion. The **drop** is responsible for cleaning up resources when a task finishes or is no longer needed. In this system, drop ensures proper resource cleanup when the task lifecycle ends.

## How do they relate to each other?  
Spawner, executor, and drop work in tandem to support efficient asynchronous operations. The spawner initiates tasks, the executor manages their execution, and drop handles cleanup once tasks are done or discarded. This coordinated workflow creates a robust and flexible environment for managing async execution in Rust.

## Before removing the drop  
![alt text](image-2.png)

## After removing the drop  
![alt text](image-3.png)

In the "before" image, the program halts at the end. This is because the drop function is triggered when a task is dropped, and it ensures that the task finishes before the program exits—guaranteeing all spawned tasks complete.

In contrast, the "after" image shows that without the drop, the program exits without waiting for the async tasks to finish. As a result, any tasks still running at the time of exit are abandoned, which can lead to incomplete operations.

