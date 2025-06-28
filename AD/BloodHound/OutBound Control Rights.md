
Whenever running BloodHound in an AD environment, we are presented with different **OutBound Controls** over  **Owned Objects**.  In this resource, I list those Outbound Control rights, discuss what they mean and how they can be abused.




## 1. HasSession


If we have HasSession over a user object, it means that have an active session on the machine. In simple terms, they are logged in. 

When a user logs in, they leave their credentials exposed. So, it is possible to extract them in plain text. 



#### Abuse Info

We can use mimikatz to extract passwords from memory in plain text. However, we would require admin rights for doing so.


```
privilege::debug

sekurlsa::logonpasswords
```





## 2. GenericAll


If we have GenericAll over an object, it essentially means that we have full control over that object. From here on, We can manipulate this object however we wish



### GenericAll over a Group

If we have full control over a Group, we can modify modify group membership of the group. We can add or remove any user we wish from that group. 


#### Abuse Info :


1. If we have 


