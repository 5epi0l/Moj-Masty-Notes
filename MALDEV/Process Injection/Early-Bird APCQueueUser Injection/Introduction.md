

## What is APC ? 

APC stands for Asynchronous Procedure Calls.  It is a function that executes Asynchronously(==without any order or chronology==) in the context of a particular thread.  The APCs are executed when certain conditions are met. These conditions are typically events or situations specified by the operating system, such as the completion of asynchronous I/O operations or other relevant events.  Here, Asynchronous means  that  the APCs can be executed at random times and don't depend on the execution of the process.


When a process queues an APC object to one of its threads, the Operating System temporarily pauses the execution flow of that particular thread to execute the queued APC function(s). When  the APC execution is completed, the thread resumes back to its normal processing.  If we suspend a thread, queue an APC to it and resume the thread again, the APC will be executed first and then the thread will be continued. 


The APC is executed when the thread enters an alertable state.  There are several ways to get a thread into alertable state such as the SleepEx function. when the thread reaches alertable state, the OS then issues a software interrupt to direct the thread execution to APC function and the wait operation returns **[WAIT_IO_COMPLETION](https://docs.microsoft.com/en-us/windows/win32/api/synchapi/nf-synchapi-waitforsingleobjectex#return-value)** and then it comes out from alertable state.

 The order of execution of APCs  is typically determined by the order in which the APCs were queued. The operating system generally follows a first-in, first-out (FIFO) order for the APC queue.
 

NOTE: When an APC is queued before  a thread begins running, the thread will call the APC function  first when it runs and then continue its normal processing.


## What is Early-Bird APC Queue injection?

In this process Injection technique, we spawn a process in a suspended state , inject our shellcode into the memory space of the suspended  process and queue the shellcode as an APC to the main thread of the process. We then  resume the main thread of the process. When the main thread is resumed, it executes our shellcode which was queued as an APC in the context of the main thread before resuming the normal execution flow of the process. That means the main thread of the process will never run because our shellcode would not finish executing and once it does, it will close the process.

An application queues an APC to a thread by calling the [**QueueUserAPC**](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-queueuserapc)function. The calling thread specifies the address of an APC function in the call to **QueueUserAPC**. 



## Procedure of APCQueueUser Injection

![[Pasted image 20240202224022.png]]



It is stealthier than CreateRemoteThread because it is normal to create APC queues in a different process than to create an entire thread.
