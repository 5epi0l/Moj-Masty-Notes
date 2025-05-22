
### The CODE:

#### First Part :


```python
import winim/lean   

proc inject[I, T](shellcode: array[I, T]): void =
	let memAddr = VirtualAlloc(
	#phandle(Not required for local process)
	NULL,                      #LPVOID lpAddress
	cast[SIZE_T](shellcode.len), #SIZE_T dwSize
	MEM_COMMIT,                 #DWORD flAllocationType
	PAGE_READWRITE              #DWORD flProtect
	)
```

The above piece of code works as below :

1. The first line `import winim/lean` imports the `winim` library which allows nim to interact 
   with the `WIN32 API`.

2. The second line is defining the main function which will be called for the injection process. 
   The `inject` keyword refers to the name of the function and it will accept an array `shellcode` as parameter . The return type of the function is `void` meaning it will not return any value after execution.

3. The third line defines a variable `memAddr` which will act as a pointer to the allocated memory space. The function `VirtualAlloc` allows us to allocate a region of memory within the current process.  
	
	1. The first Argument `lpAddress` defines where to start allocating  memory, the starting point.  If set to `NULL`, the system will choose a region of memory not in use  for allocation to place the shellcode.
	
	2.  The second argument defines the size(in bytes) of memory to be allocated. Here, the size will be equal to the size of the shellcode. The `cast` keyword is converting the datatype of length of the shellcode (`shellcode.len`) into `SIZE_T` which is accepted by the function.
	
	3.  The third argument `flAllocationType` defines the allocation type. Here `MEM_COMMIT` is used as the allocation type.
	
	4. The fourth argument `flProtect` determines the permission to set to the allocated memory space. Here `PAGE_READWRITE` is used which will allow writing into and reading from the allocated memory space. This will allow us to write our shellcode into the reserved memory space.



#### Second Part:

```python
var BytesWritten: SIZE_T

let writeProcess = WriteProcessMemory(
GetCurrentProcess(),             #HANDLE hprocess
memAddr,                         #LPVOID lpBaseAddress
unsafeAddr shellcode,            #LPCVOID lpBuffer
cast[SIZE_T](shellcode.len)      #SIZE_T nSize
addr BytesWritten                #SIZE_T *lpNumberOfBytesWritten

)
```

This is the follow up of the above code. This is how it works :

1. The first line `var BytesWritten: SIZE_T` is defining a variable of type `SIZE_T`(the type returned by the `typeof` function) which will store the amount of bytes written into the allocated memory region.

2. The second line is defining a variable `writeProcess` that will act as a pointer to the `WriteProcessMemory` function. The function takes the following arguments : 
	
	1. The first argument `hProcess` asks for a handle to the process in which we are allocating the memory bytes for shellcode injection. `GetCurrentProcess()` is an `WIN32 API` functions which gets a pseudo-handle to the current function.
	
	2. The second argument `lpBaseAddress` defines the region of memory space where the shellcode will be written/copied. This is the memory space that we have allocated within the current process. That is why, we are passing the pointer to the reserved memory address `memAddr` , asking the  function to write the shellcode into the reserved memory address.
	
	3. The third argument `lpBuffer` defines the buffer from where to write into the  reserved memory region. In this case, it will be our shellcode buffer which houses our shellcode.
	
	4. The fourth argument `nSize` determines the size of bytes to write into the reserved memory region.  This will be equal to the size our shellcode(in bytes).
	
	5. The fifth argument is an optional argument. It specifies the number of bytes written into the memory space.  The variable `BytesWritten` is passed here which is equal to the number of bytes the shellcode has.


#### Third Part: 

```python
var PrevPro: DWORD = 0

let virPro = VirtualProtect(
memAddr,                       #LPVOID lpAddress
cast[T_SIZE](shellcode.len),   #SIZE_T dwSize
PAGE_EXECUTE_READ,             #DWORD flNewProtect
addr PrevPro,                  #PDWORD lpflOldProtect
)						
```


This is the third part of the code. This is how it works :

1. The first line `var PrevPro: DWORD = 0` is declaring a variable `PrevPro` of type `DWORD` which is just unsigned double and assigning it the value `0`. This variable is will be used to store the old permissions of the reserved memory space.
 
2. The second line is invoking the `WIN32 API` function `VirtualProtect`. It allows us to alter the permissions of a given region of memory. In this case, we will be using it to alter the permissions of our reserved memory region and make it executable so that our shellcode, present in this memory space  can be executed. The main reason behind doing this in  a separate function and not in the very first function with `VirtualAlloc`  is to evade signature based EDR detections. 

3.  The arguments for the function are as follows :
	
	1.  The first argument `lpAddress` specifies the region of memory whose permissions or protections are to be changed. In our case, it is the memory location we have reserved. That is why we have passed in the pointer to the reserved memory space `memAddr` as the argument.
	
	2. The second argument `dwSize` defines the size of the memory region whose permissions are to be changed. In this case, this size will be equal to the size of the shellcode which is specified by passing `shellcode.len` as the argument.
	
	3. The third argument `flNewProtect` specifies the new permissions to be set to the memory space. We want to make our reserved location  executable so that our shellcode can be executed in memory. This permission is defined by the `PAGE_EXECUTE_READ` flag
	
	4. The fourth argument defines the older permissions of the memory space. The older permissions are stored in the variable `PrevPro`  which we had created. The address to that variable is  passed here as the argument .


#### Fourth Part: 


```python
var threadID: DWORD = 0
let threadH = createThread(
NULL,                               #LPSECURITY_ATTRIBUTES lpThreadAttributes
0,                                  #SIZE_T dwStackSize
cast[LPTHREAD_START_ROUTINE](memAddr), #lpStartAddress
NULL,                                    #lpParameter
0,                                       #dwCreationFlags
addr threadID                            #lpThreadId
)
```

This is the fourth part of the code. This is how it works :

The first line is creating a variable `threadID` which will contain the identifier for the new thread.

1.  The second  line is creating a handle to the `CreateThread` process which will create a separate thread for executing our shellcode in the current process. It takes the following arguments.

	1.   **Security Attributes (`lpThreadAttributes`):**
    
    - Think of this like setting rules for how safe or restricted the new thread is. we don't care much about specific rules,  so we are just using the default settings by providing `NULL`.
    
	2.   **Stack Size (`dwStackSize`):**
    
    - Imagine a thread as a worker who needs space to do their job. This parameter decides how much workspace (stack) that thread gets. Here, we are setting this to 0 so that they get a standard amount.
    
	3.  **Thread Function (`lpStartAddress`):**
    
    - This is the job that the new thread will be doing. It's like telling them, "Start working from this point."  Here, we are passing in the address of the memory space which has our shellcode  because we want the execution to start from this address.
    
	4. **Thread Parameter (`lpParameter`):**
    
    - Sometimes threads need specific tools or information to do their job. Here, we will set it to `NULL` to let windows decide
    
	5.  **Creation Flags (`dwCreationFlags`):**
    
    - These are special instructions for creating the new thread. For example, you might want the thread to start working right away or wait until you tell them to start. we are setting it to 0 to  execute the thread immediately after creating it.
    
	6.   **Thread ID (`lpThreadId`):**
    
    - Every (thread) has a unique ID, like an employee number. This parameter allows you to find out what that number is for the new thread . We are setting this parameter as the address of the `ThreadID` variable which has the ID of our new thread.

### Fifth Part: 

```python
defer: CloseHandle(threadH)
defer: WaitForSingleObject(threadH, int32.high)
```

The first line will close the thread handle to prevent handle leaks.

For the second line:  Our shellcode will be executed now, but the main thread of which our newly created thread is a part will terminate once the main program finishes executing. This will result in our newly created thread getting terminated even before the shellcode gets the chance to execute. To overcome this anomaly, we will have to freeze or suspend the main thread of the process and wait for our child thread to finish executing . This way, our shellcode will get enough time for execution. The  execution of the main program will return only after the child thread has finished execution of the shellcode. 



### Sixth Part :

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
        inject(shellcode)
```

1. The first line `when defined(windows):`  checks if the program is running in an windows environment.  

2.   If it is an windows env, The nested conditions  then check the architecture of the windows machine and supply the shellcode accordingly.

3. The last line checks if the program is being ran as an independent binary and not being imported. If it is being ran as an independent binary, it calls the `inject`function with the shellcode array as the argument. 


The shellcode is injected and executed in memory of the current process.

