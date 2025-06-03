
1. System Info and patches applied:

	1. `systeminfo`
	2. `systeminfo | findstr /B /C:"OS Name" /C:"OS Version" #Get only that information`
	3. `wmic qfe get Caption,Description,HotFixID,InstalledOn #Patches`
	4. `wmic os get osarchitecture || echo %PROCESSOR_ARCHITECTURE% #Get system architecture`

  
2. Check for AlwaysInstallElevated
	1. `reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated`
	2. `reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated`

		**f** these 2 registers are **enabled** (value is **0x1**), then users of any privilege can **install** (execute) `*.msi` files as NT AUTHORITY\SYSTEM

3.  If htdocs under XAMPP is running and is writable, then we can upload a shell inside htdocs and execute it via the website to gain system account
4. run `qwinsta` on the machine to display all active sessions. If there are any other active sessions, we could try to compromise it. 
5. 
