
IDOR is a common bug that occurs when an application allows users to access/modify data directly by referencing objects ( like IDs) without proper auth check


### Example

```
https://example.com/user/profile?id=123
```

If we can change id=123 to id=124 and see someone else's profile without being authorized, there is an IDOR



### NOTE

1. when looking for input points, also look for hidden input parameters within the page source
2. IDORs can also be found in cookies.