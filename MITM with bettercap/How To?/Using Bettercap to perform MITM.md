
As we have seen, `MITM` can be only be performed when the attacker has already done an `ARP spoofing attack` and placed themselves in the middle. So, the first step would be to perform an `ARP Spoofing attack`.
There are various utilities such as `arpspoof, driftnet` that can help us perform `ARP Spoofing` . However, I will be using a renowned utility called `bettercap` which is often known as the `Swiss Army Knife` for WIFI, Bluetooth Low Energy, wireless HID hijacking and MITM attacks.

However, I am only interested in MITM and following are the `bettercap modules` which will be used for MITM.
 
1. net.probe
2. arp.spoof
3. http.proxy
4. net.sniff
5. dns.spoof


I will not get into details as to what each of these modules does, It can be easily found by typing  the following in Bettercap.
```bash
help <module_name>
```

### Step 1 : Launching the Bettercap-cli

 
```bash
sudo bettercap -i <interface_name>
```

### Step 2: Listing all the devices connected to the network 


```bash
net.probe on
net.show
```

The first command will ping every IP on the network and come up with the active ones.
The second command will list the devices in a tabular format.

### Step 3: Launching the `ARP Spoofing` attack 


```bash
set arp.spoof.fullduplex true
set arp.spoof.targets <victim IP>
arp.spoof on
```

The first command will turn on the duplex mode  which will allow me to attack both the gateway and the victim(s) simultaneously.

The second command will set the target as the victim's device which will redirect  the `ARP Spoofing` attack to the victim only and will not affect other devices on the network. If the target(s) are not set, bettercap will attempt to attack the entire network.

The third option launches the attack.


### Step 4: Sniffing the packets 

```bash
net.sniff on
```
Now, every network activity of the victim will be visible on my terminal .  That means i have successfully pulled off the hack. Now if the victim logs in a to website which doesn't use `HTTPS` , I can capture their credentials in plain-text.

But MITM is not limited to capturing plain-text credentials. I can even compromise the entire system with this attack.






