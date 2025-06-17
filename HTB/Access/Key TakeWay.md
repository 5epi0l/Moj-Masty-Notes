

#### Initial Access


1. Always use `type binary` within a Microsoft FTP server whenever you need to download a binary file to avoid data corruption
2. .mdb file is a Microsoft Access Database file. We can open it using `mdbtools`





#### PrivEsc


1. Check for saved credentials in windows using :

```powershell
cmdkey /list
```



2. Use the saved credentials to run commands as that user using runas 

```powershell

runas /user:<username> /savecred "command"
```


