
### LDAP Enumeration (Anonymous Bind)

- LightWeight Directory Access Protocol is widely used for accessing and managing directory services, such as Microsoft Active Directory. 
- LDAP helps us locate and organise resources within a network, including users, groups, devices, and organisational information, by providing a central directory that applications and users can query. 
- Some LDAP servers allow anonymous users to perform read-only queries. This can expose user accounts and other directory information



We can test if anonymous LDAP bind is enabled with `ldapsearch`

```bash
ldapsearch -X -H ldap://ip -s base
```


- **-x** : Simple authentication, in our case, Anonymous auth
- **-H** : Specifies the LDAP server
- **-s** : Limits the query only to the base objects and doesn't search subtrees or children



We can then query userinfo with this command :

```bash
ldapsearch -x -H ldap://ip -b "dc=tryhackme,dc=loc" "(ObjectClass=person)"
```




We can also use `enum4linux-ng` to enumerate the Domain

```bash
enum4linux-ng -A ip -oA results.txt
```





### RPC Enumeration (NULL Session)

- Microsoft RPC is a protocol that enables a program running on one computer to request services from a program on another computer, without needing to understand the underlying details of the network.
- RPC services can be accesses over the SMB protocol


We can run the following command to verify the NULL sessions access

```bash
rpcclient -U "" ip -N
```


**-N** : Tells RPC not to prompt for a password






### RID Cycling

In Active Directoy, RID (Relative Identifier) ranges are used to assign unique identifiers to users and group objects. These RIDs are components of the Security Identifier (SID), which uniquely identifies each object within a domain. 


- 500 is the Admin Account
- 501 is the guest account
- 512-514 are for the following groups : Domain Admins, Domain Users, Domain guests
- User accounts typically start from RID 1000


If `enumdomusers` is disabled, we can manually enumerate users via RPC using the following bash command :


```bash
for i in $(seq 500 2000); do echo "queryuser $i" | rpcclient -U "" -N ip 2>/dev/null | grep -i "User Name"; done
```






### Username Enum with Kerbrute


- Kerberos is the primary authentication protocol for AD
- Kerberos uses a tickey-based system managed by a trusted third-party, the key distribution center (KDC)
- kerbrute is a popular tool that brute-force and enumerate valid AD users by abusing kerberos pre-auth
- When Kerberos pre-auth is disabled, a 