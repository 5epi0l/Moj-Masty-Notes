`API` Stands for `Application Programming Interface` and the purpose of such an interface is to essentially act as an intermediary between two pieces of software allowing them to talk to one another.

The Windows API, also known as `Win32`, is meant to provide programmers of windows application with an easy interface to access the lower level functions of the kernel.


![[Pasted image 20240107203921.png]]

As you can see in the image here, you should think of the windows OS as being arranged in layers with all the applications that users use such as paint, notepad appearing in the top layer and the drivers and the kernel down at the bottom layers. These two layers don't speak the same language, that is where the `API` comes in. The user applications, in general, don't talk directly to the kernel. In general, they talk to the `Win32` API,  the functions of which are located in `Dynamic Link Libraries` or `DLLs` such as `kernel32.dll`, `GDI32.dll`, `USER32.dll` and so on. These functions are actually abstraction of `lower level kernel functions` which are accessed through `syscalls` and are easier to access while not changing much between windows versions, while the kernel itself changes underneath of them. This makes it easier for applications to run on multiple versions of windows, although the `syscalls`, which are the language that the kernel understands might be different.  Thus, the windows API allows applications to easily speak with the kernel without needing to speak the weird , primal, otherworldly language that the kernel is accustomed to.


![[Pasted image 20240107205227.png]]

This particular syscall is `NtCreateThread` which is accessed in the backend by the `WIN32` `CreateThread` function.


![[Pasted image 20240107205617.png]]

This is another quick, simplified look at how windows manages processes.  Each process is allocated its own `Virtual Memory Space` and within that memory space are threads operating independently with their own stacks, registers etc. All process Threads are managed by the **`Thread Scheduler`** which passes the instructions to the processor making sure that all the processes get the amount of CPU they need.




## Process Injection: 

Process injection is the technique of injecting our malicious code into the memory space of an already running process. The code is then executed in the context of the process, often in a separate thread. It is one of the fundamental and well-known techniques in the realm of  Malware Development. This technique allows adversaries to bypass static and signature based detection as the code is executed in memory and nothing is written into the disk.



### Types of process Injection:

1. Local Process Injection: when the code is injected into the memory space of the current process, it is called local process injection.
2. Remote process injection: When the code is injected into the memory space of a different process, it is called remote process injection. For instance, injecting our code into the memory space of `notepad.exe` is an example of remote process injection.



### Drawbacks :

There are some drawbacks to this, especially for us as attackers. AntiViruses and EDRs can easily hook into these `WIN32` functions to detect  them when they are being called and analyse their use.

