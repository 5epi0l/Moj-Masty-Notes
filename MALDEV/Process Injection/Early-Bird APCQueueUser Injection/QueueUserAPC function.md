


The QueueUserAPC function is a native Windows API Function that is used to queue APCs to a specified thread of a process.  This function is a part of the `kernel32.dll`.  When a process needs to queue an APC object to a thread within the process or a remote process, it invokes this function. 


Syntax of QueueUserAPC function:

```C++
DWORD QueueUserAPC(
  [in] PAPCFUNC  pfnAPC,
  [in] HANDLE    hThread,
  [in] ULONG_PTR dwData
);
```


- `[in] pfnAPC`:  This argument takes the pointer of the APC function to be called. In our case, it will be the shellcode address since it has been queued as an APC function.

- `[in] hThread:`  This argument takes a handle to  the thread where the APC has been queued . In our case, it will be the main thread of the process which we spawn.

- `[in] dwData`: This argument takes any additional parameters which the APC might need for execution.

