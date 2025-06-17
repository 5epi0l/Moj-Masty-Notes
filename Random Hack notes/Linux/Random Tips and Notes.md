

1. Mounting is the process of connecting a remote file system to the local file system to access it. Any changes made to it locally would also be reflected on the remote file system.

2.  We can expose a service running on loopback to  all-interfaces using socat using the below command:

```bash
socat TCP4-LISTEN:8080,fork TCP4:127.0.0.1:<port we want to expose>
```


3. Try url encoding payloads more than once if they don't work in web-injections.


4. `7z l -slt` can provide informations about an encrypted zip archive.


5. A known plaintext attack  is when the encryption can be figured out and cracked when we know some of the plain text of the encrypted data.


6. Command to convert a powershell string to base64 
```bash
echo <powershell_string> | iconv -t utf16-le | base64 -w0 
```


7. Python one-liner to encode a url path:
```python
import urllib.parse;urllib.parse.quote(path)
```



8. Interesting files to look up when exploiting an LFI

```bash
1. /etc/passwd
2. /etc/shadow
3. /proc/self/environ
4. /proc/self/cmdline
5. /etc/apache2/sites-enabled/000-default.conf
6. /var/log/apache2/access.log
7. /var/log/auth.log
8. /home/<user>/.ssh/id_rsa
```