
## What is UAC(User Account Control)?

UAC is a security feature in Windows that allows any process to run with low privileges irrespective of the user who runs it.This policy applies to processes started by any user, including administrators themselves. The idea is that we can't solely rely on the user's identity to determine if some actions should be authorized. UAC is a **Mandatory Integrity Control (MIC)**, which is a mechanism that allows differentiating users, processes and resources by assigning an **Integrity Level (IL)** to each of them. In general terms, users or processes with a higher IL access token will be able to access resources with lower or equal ILs. MIC takes precedence over regular Windows DACLs, so you may be authorized to access a resource according to the DACL, but it won't matter if your IL isn't high enough. When a process requires to access a resource, it will inherit the calling user's access token and its associated IL. The same occurs if a process forks a child.


The following 4 ILs are used by Windows, ordered from lowest to highest:  

|   |   |
|---|---|
|**Integrity Level**|**Use**|
|Low|Generally used for interaction with the Internet (i.e. Internet Explorer). Has very limited permissions.|
|Medium|Assigned to standard users and Administrators' filtered tokens.|
|High|Used by Administrators' elevated tokens if UAC is enabled. If UAC is disabled, all administrators will always use a high IL token.|
|System|Reserved for system use.|

### Filtered Tokens

To accomplish this separation of roles, UAC treats regular users and administrators in a slightly different way during logon:

- **Non-administrators** will receive a single access token when logged in, which will be used for all tasks performed by the user. This token has Medium IL.
- **Administrators** will receive two access tokens:
    - **Filtered Token:** A token with Administrator privileges stripped, used for regular operations. This token has Medium IL.
    - **Elevated Token:** A token with full Administrator privileges, used when something needs to be run with administrative privileges. This token has High IL.

In this way, administrators will use their filtered token unless they explicitly request administrative privileges via UAC.


### UAC Settings

Depending on our security requirements, UAC can be configured to run at four different notification levels:

- **Always notify:** Notify and prompt the user for authorization when making changes to Windows settings or when a program tries to install applications or make changes to the computer.
- **Notify me only when programs try to make changes to my computer:** Notify and prompt the user for authorization when a program tries to install applications or make changes to the computer. Administrators won't be prompted when changing Windows settings.
- **Notify me only when programs try to make changes to my computer (do not dim my desktop):** Same as above, but won't run the UAC prompt on a secure desktop.  
    
- **Never notify:** Disable UAC prompt. Administrators will run everything using a high privilege token.

By default, UAC is configured on the **Notify me only when programs try to make changes to my computer** level


### UAC Internals

At the heart of UAC, we have the **Application Information Service** or **Appinfo**. Whenever a user requires elevation, the following occurs: 

1. The user requests to run an application as administrator.
2. A **ShellExecute** API call is made using the **runas** verb. 
3. The request gets forwarded to Appinfo to handle elevation.
4. The application manifest is checked to see if AutoElevation is allowed.
5. Appinfo executes **consent.exe**, which shows the UAC prompt on a **secure desktop**. A secure desktop is simply a separate desktop that isolates processes from whatever is running in the actual user's desktop to avoid other processes from tampering with the UAC prompt in any way.
6. If the user gives consent to run the application as administrator, the Appinfo service will execute the request using a user's Elevated Token. Appinfo will then set the parent process ID of the new process to point to the shell from which elevation was requested.


![[Pasted image 20240126214055.png]]

### AutoElevate

A feature called auto elevation allows specific binaries to elevate without requiring user's interaction. It can be abused for UAC Bypass. Some of these binaries which have autoelevate on are :
	1. msiconfig.msc
	2. azman.msc
	3. pkgmgr.exe
	4. spinstall.exe

 For an application, some requirements need to be met to auto-elevate:

- The executable must be signed by the Windows Publisher
- The executable must be contained in a trusted directory, like `%SystemRoot%/System32/` or `%ProgramFiles%/`

Executable files (.exe) must declare the **autoElevate** element inside their manifests. To check a file's manifest, we can use **[sigcheck](https://docs.microsoft.com/en-us/sysinternals/downloads/sigcheck)**, a tool provided as part of the Sysinternals suite.

Command :
```powershell
sigcheck64.exe -m <binary name>
```