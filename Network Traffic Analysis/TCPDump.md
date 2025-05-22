

TCPDump is a tool that we can use to capture and analyse packets in real time or from a file. It uses the **Berkley Packet Filter** (BPF) to perform traffic filtering. Since, it requires direct access to the hardware, we need to run it as root. 

TCPDump uses the libraries **pcap** and **libpcap** with an interface in promiscuous (promiscuous mode is when an interface can see all the packets being transferred on the network, along with the packets addressed to it) mode


**BASIC SYNTAX**:

```bash
sudo tcpdump -i <interface_name>
```




###  TCPDump Packet Filtering

Filtering refers to the process of getting rid of the unwanted data or "noise" acquired during the capture. It is essential as it allows us to see only the packets we're interested in. 

TCPDump has various filters that helps us accomplish this. When implemented, this filters will inspect any packets captured and look for the given values in the protocol header to match.



### Common TCPDump Filters


#### 1. Host

Used to show visible traffic destined to a given host. It is bi-directional, meaning it will show traffic sourcing from or destined to the given host


**Usage**:

```bash
sudo tcpdump host 192.168.0.1
```


#### 2. src / dest

These are modifiers. They are used to reference a source or destination host or port.


**Usage**:

```bash
sudo tcpdump src 127.0.0.1 #display only those packets which are being sent by 127.0.0.1

sudo tcpdump dst 80 #display only those packets destined for port 80
```



#### 3. net

will show traffic sourced from or destined to a given network. It used / notation


**Usage**: 

```bash
sudo tcpdump net 192.168.31.0/24 #Displays traffic destined to or sourced from this specfic network
```




#### 4. proto

Will display packets utilising a specific **protocol** type (eg, TCP, UDP, ICMP )


**Usage**:

```bash
sudo tcpdump proto http #will display only http traffic

sudo tcpdump proto icmp #will display only icmp traffic
```



P.S: 
1. proto filter can filter packets based only on network layer protocols such as TCP, UDP, ICMP.
2. To filter for an application layer protocol such as DNS or HTTP, combine proto filter with the port filter.

**Example**:

```bash
sudo tcpdump proto tcp port 80 #filters http traffic

sudo tcpdumo proto udp port 53 #filters DNS traffic
```




#### 5. Port

port is bi-directional. It displays traffic sourced to or destined from a particular port


**Usage**:

```bash
sudo tcpdump port 80 #displays traffic containing 80 as the source or destination port
```




#### 6. portrange

Allows us to specify a range of ports to filter traffic based on.


**Usage**:

```bash
sudo tcpdump portrange 1-1000 #displays all traffic sourced to or destined from ports within the specified range
```




#### 7. less / greater

less and greater can be used to look for a packet or protocol option of a specific size.

```bash
sudo tcpdump less 120 #displays packets less than 120 bytes

sudo tcpdump -i en0 'less 120' #does the same thing with an interface specified

sudo tcpdump greater 120 #display packets less than 120 bytes
```



#### 8. and / &&

and && can be used to concatenate two different filters together.


**Usage**:

```bash
sudo tcpdump 'src host 127.0.0.1 and port 80' #displays traffic originating from port 80 of the host 127.0.0.1
```




#### 9. or

or allows for a match on either of two conditions. It does not have to meet both


**Usage**:

```bash
sudo tcpdump 'src host 127.0.0.1 or port 80' #displays traffic originating from port 80 or from the the host 127.0.0.1
```



#### 10. not

filter out a specific x. eg, not UDP


**Usage**:

```bash

sudo tcpdump not ICMP #filters out all traffic utilising ICMP
```
















