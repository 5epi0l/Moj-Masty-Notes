

1. if a machine has port 88 open, its most likely a `domain controller` or a `KDC(key Distribution Centre)`

2. It is not always important that a Domain Controller will act as a KDC. We can have a KDC which is not a domain controller but it is rare in corporate networks.

3. port 389 runs `LDAP` which stands for `Light Weight Directory Access Protocol`. It is the protocol by which AD works, effectively.

4. port 636 runs `LDAP` over `SSL`. 

5. port 53 runs `DNS` over `TCP`

6. A Domain Controller often acts as the DNS server for the domain.

7.  Windows AD networks prefer communicating via Host names than IP addresses. Kerberos wants hostnames.

8. Most effective tools in AD hacking : 
	1.  CrackMapExec(CME)
	2.  Responder
	3.  BloodHound
	4.  Nmap

9.  CrackMapExec can be used on multiple hosts. We can run CME on an entire subnet or an IP range.


10. SMB signing means whenever we send an SMB message, the server requires us to sign it so that we cannot be modified in transit. This prevents `NTLMrelay`.


11. When testing an AD environment, always test for `NULL Authentication` .NULL Authentication refers to the ability to access resources or services without providing any username or password, essentially allowing anonymous access. A poorly configured server might allow `NULL Authentication`.

12. If we have any usernames, we can probably try a Password Spray on kerberos at port 88.

13. If we have a list of usernames for an AD env, we can find out the valid usernames with a tool called `kerbrute`.  It makes use of `Kerberos Pre-authentication` to enum valid users.  Using it needs access to one of the DCs. 

14.  kerberos allows us to send requests as a user and depending on how the DC responds, we can determine if the user exists or not.

15. When we have a list of valid usernames for an AD, we can perform an`ASREPRoast` to capture the password hash for the users. `ASREProasting` works only when Kerberos Pre-Authentication is disabled which requires the users to prove their identity to the KDC before receiving a TGT.

16. We can use impacket's `Get-NPusers.py` script to perform an `ASREPRoast`.

17. An RID(Relative Identifier) is the last part of a hyphen separated SID(Security Identifier). It uniquely identifies  principals within the domain SID. RIDs are relatively small and we can iterate through them to identify which principals exist.

18. We can brute force RIDs using CME. Doing it provides us with all the users and groups present in the domain.

19. CME gets the User SID by logging in as the user and the User SID contains the Domain SID.

20.  A `SPN` is a Service Principal Name. All services have SPNs assigned to them. It acts like a unique identifier for that service.

21. We can get all usernames which have SPN set using impacket's `GetUserSPNs.py` script.  

22.  Shellcode cannot be executed in its encoded form. it needs to be decoded at runtime.

23.  To decode at runtime, a decoder stub is used . The reason why encoded shellcodes get detected is because of the decoder stub which maybe signatured.

24. The stdapi in meterpreter is the standard API which holds all the function definitions which help meterpreter access the windows API and do its magic.

25. 90% of the time  , stdapi is what gets caught by defender when meterpreter tries to load it.

26. The msf generated ssl certificate is also signatured, that is why it is recommended to use a custom SSL cert to increase the chances of AV evasion.

27.  

28.  When getting initial foothold as an user, we have a valid token which is stored in lsass. So, we can run bloodhound from here and it will work because we are in a valid domain user session.

29. SharpChromium dumps user creds from chrome. (https://github.com/djhohnstein/SharpChromium)

30.  If we try to run a .msi file from a session 0 shell, it will not work.  Session 0 means that our current context is not interactive. We’re spawning out of something that is meant to be a non-interactive service on the host. So, to run a .msi file, we need to be in a session 1.

31.  COM stands for Component Object Model. It allows communication between various software components to make sure they work seamlessly. It provides a set of rules and conventions that allow different pieces of software to interact and share functionality without knowing the intricate details of each other.

32. DCOM stands for Distributed Component Object Model. It is an extension of the Component Object Model (COM) and is designed to enable communication and interaction between software components across a network. COM only allows communication between software components within a single system whereas DCOM allows the same over different machines in a network.

33.  There is a method called `ExecuteShellCommand` within DCOM which we can access over the network and it allows us to execute commands.  Hence, we can use this method to achieve code execution on a remote host.  The user will need Admin privileges on the Host machine to access the MMC 2.0 method and also Admin privileges on the Remote machine to execute. See [[Useful commands]] for "howto"



34. HTA's are standalone applications that execute using the same models and technologies of Internet Explorer, but outside of the browser.Mshta is a utility that executes Microsoft HTML Applications (HTA). HTA files have the file extension `hta`. We can execute a malicious hta file hosted on a remote server using mshta to achieve code execution. See [[Useful commands]] for "howto"



35. We can use schtasks binary within windows for persistence. The command below adds a scheduled task which will execute a file named `pwn.exe` after ever minute. The last command just deletes the scheduled task. This is a common way of achieving persistence. See [[Useful commands]] for commands.


36. Persistence can be achieved using a binary called `SharPersist` which utilises many techniques such as scheduled tasks, reg key modification to achieve persistence on a compromised host.


37.  Persistence can be achieved by modifying the registry keys so that it would execute our beacon, eveytime the user logs in to their work station . See [[Useful commands]] for commands.

39. Windows shortcuts can be used to run malware.

40. We can combine an exe file with a jpg file using winrar to deceive a user into executing our malware. When the user clicks on the image, the image will open but in the backend our malware will get executed.

41.  RID Bruteforcing can be done if we have read access to the `IPC$` share.  It's essentially and SMB exploit where if we have read access to the `IPC$` share, we can enumerate domain users by iterating through their RIDs.



42.  There’s a DLL called **comsvcs.dll**, located in `C:\Windows\System32` that **dumps process memory** whenever they **crash**. This DLL contains a **function** called `**MiniDumpW**` that is written so it can be called with `rundll32.exe`. We can use this function to dump the lsass memory which contains the logonPasswords and then extract them with mimikatz.


	

43. When Windows opens a file, it checks the registry to know what application to use. The registry holds a key known as Programmatic ID (**ProgID**) for each filetype, where the corresponding application is associated. Let's say you try to open an HTML file. A part of the registry known as the **HKEY_CLASSES_ROOT**will be checked so that the system knows that it must use your preferred web client to open it. The command to use will be specified under the `shell/open/command` subkey for each file's ProgID

44. In Windows, a programmatic identifier (_ProgID_ ) is a registry entry that the Windows Shell uses  to associate a file type with an application, and to control the behavior of the association.


45. When checking HKEY_CLASSES_ROOT, if there is a user-specific association at **HKEY_CURRENT_USER (HKCU)**, it will take priority. If no user-specific association is configured, then the system-wide association at **HKEY_LOCAL_MACHINE (HKLM)** will be used instead.


46. `CurVer` is an element used in a progID subkey.  `CurVer` entry is used to set the default version of an application if multiple other applications are found on the system.


47. Scheduled tasks , by design, are meant to be run without any user interaction (independent of the UAC security level).




48. An Alternate Data Stream (ADS) is a feature in the NTFS (New Technology File System) file system used by Windows operating systems. NTFS allows for the creation of multiple data streams within a single file. The main or primary data stream contains the actual file data, while alternate data streams can store additional metadata, extended attributes, or even entirely separate sets of data. Attackers might use alternate data streams to hide data or code within a file without affecting its main functionality or appearance.


49. UserDescriptions in a DC may contain Potential Passwords for the users. 


50. A thread is the smallest unit of execution within a process. It is a component of a process that runs independently and is able to execute instructions within the process independently of the other threads running within the process.

51. PowerShell has a method for storing encrypted credentials that can only be accessed by the user account that stored them. To retrieve the credential and using it within a script, you read it from the XML file.

52. We can make a trojan with iexpress software in windows. 


53. Local Administrator Password Solution (Windows LAPS) is a Windows feature that automatically manages and backs up the password of a local administrator account on your Microsoft Entra joined or Windows Server Active Directory-joined devices to the DC. We can dump the LAPs to get access to admin passwords.


54. An [_access token_](https://learn.microsoft.com/en-us/windows/desktop/SecGloss/a-gly) is an object that describes the [_security context_](https://learn.microsoft.com/en-us/windows/desktop/SecGloss/s-gly) of a [_process_](https://learn.microsoft.com/en-us/windows/desktop/SecGloss/p-gly) or thread. The information in a token includes the identity and privileges of the user account associated with the process or thread. When a user logs on, the system verifies the user's password by comparing it with information stored in a security database. If the password is [_authenticated_](https://learn.microsoft.com/en-us/windows/desktop/SecGloss/a-gly), the system produces an access token. Every process executed on behalf of this user has a copy of this access token.



55. If a service account has been stripped off its juicy privileges such as `SeImpersonatePrivilege` or `SeAssignPrimaryToken` , we can set a scheduled task as the service account to send us a shell and the shell will contain all the privs we need to escalate further. 


56. If a privilege is disabled, it can be enabled with a tool called `FullPowers.exe`.  If it does not work, we can also try using `RunasCs.exe` which takes in a username and password for a user to spawn a process as that user.



58. The `SeDebugPrivilege` in windows allows us access to any process be it high IL or low IL. That means , we can access a process and its threads spawned by system as a normal user if we have `SeDebugPrivelege` enabled.  That means, we can inject shellcode or DLL into a high IL process to elevate privileges if `SeDebugPrivilege` is enabled. 



59. When  you find usernames, always use the usernames as passwords for the usernames to check if we can login. (kerbrute --user-as-pass).


60. The SAM file is locked while the system is running. So, if we discover an LFI we cannot access the SAM file even if the webservice is running as SYSTEM. However, the backups of the SAM hive could still be available in `C:\Windows\System32\config\RegBack`


61. It is possible to execute any random binary/command whenever the user logs in to the pc by creating a new value in the registry of winlogon. 