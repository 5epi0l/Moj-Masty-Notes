


### Wireshark 101

-  wireshark is a free and open-source network traffic analyzer much like tcpdump, but with a GUI.
- Wireshark can capture data from multiple interfaces such as wifi, bluetooth


### TShark

-  TShark is a cli based rendition of wireshark. 
- It shares many of the same features that are included in wireshark
- perfect for environments with no GUI.
- Uses the same filtering syntax as BPF



### Wireshark capture filters


**Captures filters are entered before the capture is started**



#### 1. host

Captures traffic pertaining to a certain host



#### 2. net 

captures traffic to or from a specific network (using slash notation to specify the mask)



#### 3. not port 

will filter out all traffic except the port specified



#### 4. port X and Y

AND will concatenate the specified ports



#### 5. portrange x-y

used to specify a portrange



#### 6. ip / ether / tcp

grabs traffic from specified protocol headers



#### 7. Broadcast / multicast / unicast

Grabs a specific type of traffic, one to one, one to many, one to all







### Wireshark Display Filters



**Display Filters are used while the capture is running and after the capture has stopped**




#### 1. ip.addr == x.x.x.x

```bash
Capture only traffic pertaining to a certain host. This is an OR statement
```



#### 2. ip.addr == x.x.x.x/24

```bash
Capture only traffic pertaining to a specific network. This is an OR statement
```



#### 3. ip.src / dst == x.x.x.x

```bash
captures to or from a specific host
```



#### 4. dns / tcp / ftp / arp / ip

```bash
Filter traffic by a specific protocol
```




#### 5. tcp.port == x / udp.port == x

```bash
filter by a specific tcp port or udp port
```




#### 6.  tcp.port / udp.port != x

```bash
will capture everything except the port specified
```



#### 7. and / or / not

```bash
AND will concatenate, OR will find either of two options, NOT will exclude
```




### Advanced WireShark Shit



#### Plugins:

Plugins can give us detailed reports about the network traffic being utilized. It can show us everything from top talkers in our environment to specific conversations and even breakdown by IP and protocols


From the Analyze Tab, we can utilize plugins that allows us to do things like following a TCP stream, filter on conversation types, prepare new packet filters 



#### 1. Following TCP streams


Wireshark can follow TCP streams to stitch together TCP packets to recreate the entire stream in a readable format. This allows us to pull data (image, files, etc) out of the capture.





#### 2. FTP Dissector (Full List [Here](https://www.wireshark.org/docs/dfref/f/ftp.html))


- **ftp.request.command** 

```bash
will show any commands sent across the ftp-control channel (port 21)

we can look for information like usernames
```



- **ftp-data**

```bash
Will show any data transferred over the data channel (port 20)

Using this filter, we can reconstruct anything transferred by placing the raw data back into a new file.
```






