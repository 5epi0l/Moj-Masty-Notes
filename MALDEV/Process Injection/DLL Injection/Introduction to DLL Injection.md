


## What are DynamicLink Libraries or DLLs?


Dynamic Link Libraries are libraries  that contain code and data that can be used by more than one program at the same time. DLLs are designed to allow multiple programs to use the same code and resources simultaneously, promoting code reusability and efficient memory usage.
DLLs allow **dynamic linking,** meaning that the code and data in the DLL are not embedded directly into the program executable but are linked at runtime. This helps in reducing the size of the executable and allows for updates to the shared code without requiring changes to every program using it.



## What is DLL Injection?


DLL Injection refers to the injection of a DLL into the memory space of a remote process.
When the DLL is injected into a remote process, it gets executed in the context of that process. 


## Library Loading in windows.

In windows Environment, if any process has to load any DLL within it. It will have to call the `LoadLibraryA` API function which is a part of `kernel32.dll` . **LoadLibrary** can be used to load a library module into the address space of the process and return a handle. 

Note: ***LoadLibrary** can also be used to load other executable modules such as .exe files.


```C++
HMODULE LoadLibraryA(
  [in] LPCSTR lpLibFileName
);
```


The LoadLibraryA function accepts a Filename as the argument and this filename will be the name of the DLL to be Loaded.  If the specified DLL is not already loaded for the calling process, the system calls the DLL's [DllMain](https://learn.microsoft.com/en-us/windows/desktop/Dlls/dllmain) function ,which is the entry point for the DLL,(==*DllMain can be thought of as an equivalent of the main function in C*==) with the **DLL_PROCESS_ATTACH** value. If **DllMain** returns **TRUE**, **LoadLibrary** returns a handle to the module. If **DllMain** returns **FALSE**, the system unloads the DLL from the process address space and **LoadLibrary** returns **NULL**.



## Kill Chain of a remote DLL Injection.

DLL Injection utilises API functions of the win32 API , below is a general killchain :

- OpenProcess() : The OpenProcess() function is Invoked to gain a handle of the process in which the DLL will be injected. It accepts the PID of the remote process and opens a handle to it, allowing us to interact with the process via the handle.


-  VirtualAllocEx() : After opening a handle to the remote process, the next task would be to allocate memory to inject the DLL  within the memory space of the remote process.  The VirtualAllocEx() function is used to allocate memory within a remote process. It accepts the handle to the process in which memory is to be allocated. 


- WriteProcessMemory() : After allocating memory in the remote process, the WriteProcessMemory function is Invoked to write the contents of the DLL into the memory space.


- GetModuleHandleA() : After writing the DLL into memory, we now need to load it using LoadLibraryA. But for that, we will need a handle to kernel32.dll to get the memory address of `LoadLibraryA` function  . To get the handle to kernel32.dll , the function `GetModuleHandleA` is invoked with `kernel32.dll` as the argument.


- GetProcAddress() : After receiving a handle to `kernel32.dll` , the next step is to the get the address of the `LoadLibraryA` function from the handle. To do so, the GetProcAddress() function is invoked with the handle to the `kernel32.dll` and the function name `LoadLibraryA` as the argument. 


- CreateRemoteThread() : After the address to the `LoadLibrayA` function has been obtained, we can now execute the DLL present in the memory space using this function in a separate thread. For this, the `CreateRemoteThread()` function is Invoked which executes the DLL in memory in a separate thread. 