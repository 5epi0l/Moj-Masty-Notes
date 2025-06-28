
Whenever running BloodHound in an AD environment, we are presented with different **OutBound Controls** over  **Owned Objects**.  In this resource, I list those Outbound Control rights, discuss what they mean and how they can be abused.




## 1. HasSession


If we have HasSession over a user object, it means that have an active session on the machine. In simple terms, they are logged in. 

When a user logs in, they leave their credentials exposed. So, it is possible to extract them in plain text. 



#### Abuse Info

We can use mimikatz to extract passwords from memory in plain text. However, we would require admin rights for doing so.



## 2. GenericAll


If we have GenericAll over an object, it essentially means that we have full control over that object. From here on, We can manipulate this object however we wish



### GenericAll over a Group

If we have full control over a Group, we can modify modify group membership of the group. We can add or remove any user we wish from that group. 


#### Abuse Info :


1. Changing the group ownership with impacket-owneredit
2. Granting FullControl over the group with impacket-dacledit
3. Adding Ourselves to that group using : 
	1. BloodyAD
	2. pth-net
	3. ldapmodify
	4. ldap_shell



### GenericAll Over a User

If we have Full Control over a user, we can reset their password. We can also write to the  **msds-KeyCredentialLink**  attribute of a user. Writing to this property allows an attacker to create “Shadow Credentials” on the object and authenticate as the principal using Kerberos PKINIT

Alternatively, we can write to the “servicePrincipalNames” attribute and perform a targeted kerberoasting attack.



#### Abuse Info

1. Change the OwnerShip of the User with impacket-owneredit
2. Granting FullControl over the User with impacket-dacledit
3. Reseting the password 



### GenericAll Over a Computer


If we have full Control over a workstation, we can dump the LAPS password. 
Alternatively, Full control of a computer object can be used to perform a Resource-Based Constrained Delegation attack











