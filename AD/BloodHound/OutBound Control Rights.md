
Whenever running BloodHound in an AD environment, we are presented with different **OutBound Controls** over  **Owned Objects**.  In this resource, I list those Outbound Control rights, discuss what they mean and how they can be abused.




### 1. HasSession


If we have HasSession over a user object, it means that have an active session on the machine. In simple terms, they are logged in. 

When a user logs in, they leave their credentials exposed. So, it is possible to extract them in plain text. 



#### Abuse Info

We can use mimikatz to extract passwords from memory in plain text. However, we would require admin rights for doing so.


```
privilege::debug

sekurlsa::logonpasswords
```





