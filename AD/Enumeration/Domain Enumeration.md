
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
- 