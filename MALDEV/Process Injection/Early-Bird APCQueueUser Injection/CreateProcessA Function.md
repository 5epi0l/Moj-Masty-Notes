
The CreateProcessA function is a native windows API function which can be used to create a new process and its primary thread. The new process runs in the security context of the calling process. This function is preferred to the `StartProcess()` function in nim because it returns a handle to the process and the primary thread. As a result, we don't need to Invoke `OpenProcess()` to get handles to the process and the thread which makes it stealthy.


```C++
BOOL CreateProcessA(
  [in, optional]      LPCSTR                lpApplicationName,
  [in, out, optional] LPSTR                 lpCommandLine,
  [in, optional]      LPSECURITY_ATTRIBUTES lpProcessAttributes,
  [in, optional]      LPSECURITY_ATTRIBUTES lpThreadAttributes,
  [in]                BOOL                  bInheritHandles,
  [in]                DWORD                 dwCreationFlags,
  [in, optional]      LPVOID                lpEnvironment,
  [in, optional]      LPCSTR                lpCurrentDirectory,
  [in]                LPSTARTUPINFOA        lpStartupInfo,
  [out]               LPPROCESS_INFORMATION lpProcessInformation
);
```

