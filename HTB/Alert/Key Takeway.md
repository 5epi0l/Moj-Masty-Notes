
### 1. If we have an xss, and cannot exfil cookie. Try to perform CSRF via XSS


### 2. When privilege escalating, look for writable files in interesting dirs (such as /var/www or config files) with the following command :


```bash
find . -writable
```