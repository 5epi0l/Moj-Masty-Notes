
## What is MITM?

`MITM` , short for `Man-In-The-Middle`,  is a network attack done on a Local Network in which the attacker can intercept, eavesdrop or even modify network packets flowing between two hosts.
The attacker does so by putting himself in between the two devices and making the traffic to go through the attacker's computer.

![[Pasted image 20240103011214.png]]
## How is MITM performed?

`MITM` is performed with the help of another local network attack called `ARP Poisoning`. `ARP`
stands for `Address Resolution Protocol`.

### ARP:

 It is a protocol which helps devices on a network to locate other devices and facilitate data transfer. When a sender device doesn't know where its receiver device is , it sends out an ARP broadcast request pinging every host on the network. When the host , the machine has been looking for receives the broadcast packets, it sends a unicast `ARP response` to the device from which the broadcast packet originated.  In this way, the sender is able to determine the address of the receiver .

An ARP packet contains the sender's IP , sender's mac, receiver's IP and in the receiver's mac is the broadcast mac address `ff:ff:ff:ff:ff:ff`.

### ARP poisoning/Spoofing :

`ARP Poisoning` is a type of cyberattack carried out over a `Local Area Network` that involves sending malicious ARP packets to the `default gateway AKA The Router` in order to change the pairing in its ARP to MAC address table, thus poisoning the `ARP cache`.

The attacker sends specially crafted `ARP` packets to both the gateway and the target. For the packets flowing from the victim to the gateway, the forged ARP packets sent by the attacker look like this :

![[Pasted image 20240102234544.png]]



And for the packets flowing from the gateway to the victim, the forged ARP packets look like this:

![[Pasted image 20240102234722.png]]

This allows the attacker to place themselves in between the victim and the gateway. The attacker can eavesdrop, manipulate packets, drop packets, capture clear-text passwords and hijack sessions among other things.

