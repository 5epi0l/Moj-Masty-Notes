
One of the first things to do when given access to an internal network, is to look for active hosts that we can target. We can do this in multiple ways. Here is a way of discovering hosts using fping,


```bash
fping -agq <subnet>
```


- **-a** : show systems that are alive
- **-g** : generates a target list from a supplied IP netmask
- **-q** : quiet mode, doesn't show per-probe results





## Common Ports in AD

| Port | Protocol          | What it means                                 |
| ---- | ----------------- | --------------------------------------------- |
| 88   | kerberos          | Potential for kerberos-based enumeration      |
| 135  | MS-RPC            | Potential for RPC enumeration (null sessions) |
| 139  | SMB/NetBIOS       | Legacy SMB access                             |
| 389  | LDAP              | LDAP queries to AD                            |
| 445  | SMB               | Modern SMB access, critical for enum          |
| 464  | kerberos(kpasswd) | Password-related kerberos service             |


We can spot the DC because it will often have ports like 88, 389, 445 open with banners like 'Windows Server'