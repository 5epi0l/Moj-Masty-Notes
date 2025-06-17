
1. If we have a squid HTTP proxy running on a machine, we can try to enumerate services behind the proxy through proxychains

```bash
#proxychains.conf VER 3.1
--SNIP--
http <proxy_ip> <proxy_port>
```

After adding this line to the proxychains config, we can run nmap with proxychains to uncover services behind the proxy. 

```bash
proxychains nmap -p- 127.0.0.1
```


2. tftp can be used to upload files to a ftp server if the normal ftp client doesn't work

3. If we there is a cronjob running as root that runs `apt`, we can put a malicious file under `/etc/apt/apt.conf.d`, and everytime the cronjob runs, it will execute our malicious file