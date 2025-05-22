
In remote process injection, we inject our shellcode into the memory space of a remote process. The approach almost remains similar to shellcode injection into the current process, with a few modifications.

Below is the code for injecting shellcode into a remote process.


### The Code:

#### First snippet:

```python

import winim/lean
import osproc
import strformat #optional

proc injectRemote[I, T](shellcode: array[I, T]): void =
	let injectedProcess = startProcess("notepad.exe")
	injectedProcess.suspend()
	let PID: DWORD = cast[DWORD](injectedProcess.processID)
```

#### Explanation:

1. The first three lines are importing three modules, `winim, osproc and strformat which is optional.` The first module enables nim to talk to the windows API, the second module basically provides os processing functionalities such as running system commands, starting processes etc. And the last module, which is optional here, helps us format our strings better.

2. The second line is defining the main function which will be called for the injection process. 
    The `injectRemote` word  refers to the name of the function and it will accept an array `shellcode` as parameter . The return type of the function is `void` meaning it will not return any value after execution.

3.  The third line `let injectedProcess = startProcess("notepad.exe")` is using the function `startProcess` from the module `osproc` to spawn up a process which we will use to inject our shellcode.  `injectedProcess` is the pointer to the spawned process.

4. `injectedProcess.suspend()` suspends `notepad.exe` after spawning. This is done to halt the execution of the program when executing the shellcode. It basically prevents `notepad.exe` from popping up.

5. The last line just gets the Process ID of `notepad.exe` and stores it within a variable `PID` . This will be used by other functions later on.


#### Second Snippet:

```python
let pHandle = OpenProcess(
PROCESS_ALL_ACCESS,
false,
PID
)

let memAddr = VirtualAllocEx(
pHandle,
NULL,
cast[SIZE_T](shellcode.len),
MEM_COMMIT,
PAGE_READWRITE

```

#### Explaination:

This snippet opens up a handle to the process `notepad.exe` using the function `OpenProcess` by passing in the `PID` of the process. Then a suitable amount of memory is allocated in the remote process using the function `VirtualAllocEx`. The function takes the  handle to the process `pHandle` and allocates memory within its memory space equal to the length of the shellcode(in bytes). It then alters the permissions/protections of the allocated memory region making it readable and  writable using the flag `PAGE_READWRITE` so that we can write our shellcode within it.


#### Third Snippet:


```python
var BytesWritten: SIZE_T

let writeProc = WriteProcessMemory(
pHandle,
memAddr,
unsafeAddr shellcode,
cast[SIZE_T](shellcode.len),
addr BytesWritten
)
```


#### Explanation:

This piece writes our shellcode into the allocated memory space of  the remote process `notepad.exe` using the function `WriteProcessMemory()`.  It takes the handle to the process `pHandle` , the address of the allocated memory space `memAddr` and the address of the shellcode buffer where our shellcode is stored.
Then it writes the shellcode into the memory space in bytes  equal to the size of the shellcode.


#### Fourth Snippet:

```python
var PrevPro: DWORD = 0

let virPro = VirtualProtectEx(
pHandle,
memAddr,
cast[SIZE_T](shellcode.len),
PAGE_EXECUTE_READ,
addr PrevPro
)
```


#### Explanation:

1. The first line `var PrevPro: DWORD = 0` is declaring a variable `PrevPro` of type `DWORD` which is just unsigned double and assigning it the value `0`. This variable is will be used to store the old permissions of the reserved memory space.

2. The second line is invoking the `WIN32 API` function `VirtualProtecEx`. It allows us to alter the permissions of a given region of memory in a remote process. In this case, we will be using it to alter the permissions of our reserved memory region and make it executable so that our shellcode, present in this memory space  can be executed. 

3. The arguments for the function are as follows :

	1. The first argument `lpAddress` specifies the region of memory whose permissions or protections are to be changed. In our case, it is the memory location we have reserved. That is why we have passed in the pointer to the reserved memory space `memAddr` as the argument.
	
	2. The second argument `dwSize` defines the size of the memory region whose permissions are to be changed. In this case, this size will be equal to the size of the shellcode which is specified by passing `shellcode.len` as the argument.
	
	3. The third argument `flNewProtect` specifies the new permissions to be set to the memory space. We want to make our reserved location  executable so that our shellcode can be executed in memory. This permission is defined by the `PAGE_EXECUTE_READ` flag
	
	4. The fourth argument defines the older permissions of the memory space. The older permissions are stored in the variable `PrevPro`  which we had created. The address to that variable is  passed here as the argument . 


#### Fourth Snippet:

```python
var threadID: DWORD = 0

let threadH = CreateRemoteThread(
pHandle,
NULL,
0,
cast[LPTHREAD_START_ROUTINE](memAddr),
NULL,
0,
addr threadID
)
```

#### Explanation:

1.  The first line is creating a variable `threadID` which will contain the identifier for the new thread.

2. The second  line is creating a handle to the `CreateRemoteThread` function which will create a separate thread in the remote process `notepad.exe` for executing our shellcode . It takes the following arguments.
	
	1.  .  **Security Attributes (`lpThreadAttributes`):**
    
    - Think of this like setting rules for how safe or restricted the new thread is. we don't care much about specific rules,  so we are just using the default settings by providing `NULL`.
    
	2.   **Stack Size (`dwStackSize`):**
    
    - Imagine a thread as a worker who needs space to do their job. This parameter decides how much workspace (stack) that thread gets. Here, we are setting this to 0 so that they get a standard amount.
    
	3.  **Thread Function (`lpStartAddress`):**
    
    - This is the job that the new thread will be doing. It's like telling them, "Start working from this point."  Here, we are passing in the address of the memory space which has our shellcode  because we want the execution to start from this address.
    
	4. **Thread Parameter (`lpParameter`):**
    
    - Sometimes threads need specific tools or information to do their job. Here, we will setting it to `NULL` to let windows decide
    
	5.  **Creation Flags (`dwCreationFlags`):**
    
    - These are special instructions for creating the new thread. For example, you might want the thread to start working right away or wait until you tell them to start. we are setting it to 0 to  execute the thread immediately after creating it.
    
	6.   **Thread ID (`lpThreadId`):**
    
    - Every (thread) has a unique ID, like an employee number. This parameter allows you to find out what that number is for the new thread . We are setting this parameter as the address of the `ThreadID` variable which has the ID our new thread.




#### Fifth Snippet:

```python
defer:CloseHandle(pHandle)
defer:CloseHandle(threadH)
```


This two lines will terminate the handles once our shellcode has been executed in memory.


#### Sixth Snippet:

```python
when defined(windows):

    when defined(i386):
        var shellcode: array[size, byte] = [
        byte #[32-bit shellcode here]#
        ]

    elif defined(amd64):
        var shellcode: array[size, byte] = [
        byte #[64-bit shellcode here]
        ]

    when isMainModule:
        injectRemote(shellcode)
```

. The first line `when defined(windows):`  checks if the program is running in an windows environment.  

2.   If it is an windows env, The nested conditions  then check the architecture of the windows machine and supply the shellcode accordingly.

3. The last line checks if the program is being ran as an independent binary and not being imported. If it is being ran as an independent binary, it calls the `inject`function with the shellcode array as the argument. 


The shellcode is injected and executed in memory of the remote process.
