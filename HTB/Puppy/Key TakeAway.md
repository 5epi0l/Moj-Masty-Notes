
#### Initial Foothold

###### Enabling a disabled user via LDAP

We can enable a disabled user in AD via LDAP with `ldapmodify` if we have Ownership of their user object or have access to a user account that has the privilege to make AD changes.


1. Find the DN of the target User

```bash
ldapsearch -x -H ldap://<IP> -D "username@domain" -W -b "dc=domain,dc=com" "(sAMAccountName=<username>)"
```




2. creating a `.ldif` file

An LDIF file is a plain text file used to represent directory entries in a format that can be read and processes 

We need to reset the `UserAccountControl` attribute  of the user object.

- Normal (enabled) :  512
- Disabled : 514


```python
dn: <DN of the User retrieved from LDAP>
changetype: modify
replace: UserAccountControl
UserAccountControl: 512
```




3. Running `ldapmodify` to enable the user account

```bash
ldapmodify -x -H ldap://<ip> -D "username@domain" -W -f <ldif_file>
```





PS : - We **cannot** enable a disabled account if we **only** have its password. we must authenticate with **another user that has privileges to make AD changes** or a user that has ownership of our target user object. 





