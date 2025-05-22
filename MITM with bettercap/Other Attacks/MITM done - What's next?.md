
Once the attacker has successfully performed an `MITM` attack, they can perform a variety of other attacks such as :

1. `DNS Spoofing`
2. `JavaScript Injection`
3. `HTTPS downgrading or SSL stripping`
4. `Stealing Cookies`
5. `Sniff Password`
6. `Denial Of Service(DOS)`
7. `Malware Injection`



## 1. DNS Spoofing:

`DNS` stands for Domain Name System. DNS resolves a domain to its IP as browsers understand only IP. When we enter a FQDN in a browser, the browser sends a requests to a dns server asking for the IP address of the entered domain. The DNS server then checks its records and reply back with the IP address. The browser then accesses the IP address and loads our website.

When the browser receives the IP address of the FQDN, it stores the IP address in its DNS cache. When the FQDN  is entered again, the browser takes the IP address from its DNS cache instead of connecting to a DNS server . `DNS spoofing` or `DNS poisoning` refers to the attack in which an attacker poisons that DNS cache and alters the Domain to IP mappings.  This enables the attacker to redirect the victim to any website of the attacker's choice.

![[Pasted image 20240103010832.png]]




## 2. JavaScript Injection :

JavaScript injection in the context of a Man-in-the-Middle (MITM) attack refers to the unauthorized insertion of malicious JavaScript code into web pages that are being transmitted between a user and a website. In a MITM attack, an attacker intercepts and manipulates the communication between a user and a website, allowing them to modify the content of web pages before they reach the user's browser. This attack can allow attackers to steal cookies or redirect users to phishing websites.


### 3. HTTPS Downgrading or SSL Stripping : 


`HTTPS Downgrading` or `SSL Stripping` involves manipulating the communication between a user and a web server by rerouting the traffic flowing through a TLS (Transport Layer Security) tunnel via an attacker controlled  proxy. The goal is to intercept and capture the transmitted data in plain-text format. This attack enables the attacker to access the content even when the user and the web server are originally communicating through an encrypted TLS channel. By downgrading the connection to an unencrypted state, the attacker can effectively eavesdrop on sensitive information exchanged between the user and the server.

![[Pasted image 20240103231634.png]]


### 4. Stealing Session token AKA Cookies 

Once the attacker has successfully performed an `MITM` attack, they can now steal `session tokens` or `Cookies` of the user and perform account takeover. `Sessions` are used by web servers to identify the user they are interacting with and to determine whether the user is logged in or not. The user sends their `session token` with each web request to the webserver. An attacker sitting in middle of the transmission can easily sniff the `cookie` and use it to authenticate on behalf of the user. Even if the transmission channel is encrypted, the attacker can perform an SSL stripping attack to get the sessions in plain text.


### 5. Stealing passwords in plain-text: 

The attacker can also steal user credentials using an `MITM` attack. An attacker eavesdropping on the conversation between the user and the server can capture `credentials` i.e usernames and passwords in plain-text if the victim logs in to a website, essentially compromising the victim's account. Even if `TLS` is in place, the attacker can employ an `SSL Stripping attack` and downgrade the `HTTPS` traffic into plain-text format to capture the credentials.


### 6. Denial Of Service(DOS):

`A Denial of Service (DoS)` attack involves an attacker attempting to overwhelm a system by flooding it with a high volume of data packets, rendering it temporarily or permanently unavailable. The primary objective of a `DoS` attack is to disrupt the normal functioning of a targeted system or network.

In the context of a `Man-in-the-Middle (MITM)` attack, a `DoS` attack can indeed be directed at the `gateway`. The `gateway`, being the central point for data transfer within a network, plays a crucial role in mediating communication between devices. By inundating the `gateway` with an excessive amount of traffic, an attacker aims to saturate its capacity and cause a halt in the transfer of data within the network.

Taking down the `gateway` in a `MITM` scenario essentially cripples the entire network, as all devices are connected through it. This can lead to a loss of connectivity, disruption of services, and potentially impact the functionality of devices within the network. It's important for network administrators to implement security measures to mitigate the risks associated with DoS attacks, such as traffic filtering, rate limiting, and intrusion detection systems.


### 7.Malware Injection:

Malware injection in the context of a Man-in-the-Middle (MITM) attack involves introducing malicious code or software into the communication between a user and a server.  The attacker positions themselves between the user and the server, intercepting the data traffic passing between them.  The attacker identifies a specific target within the intercepted communication, such as a file download, a software update, or a webpage the user is accessing. The attacker analyzes the intercepted traffic to understand the structure and content of the communication. This may involve looking for vulnerabilities or weak points in the targeted data. Based on the identified vulnerabilities, the attacker creates a malicious payload. This could be malware-laden files, scripts, or code designed to exploit weaknesses in the target system.  The attacker injects the malicious payload into the intercepted communication. This injection can happen in various ways, such as modifying the content of a file being downloaded, altering a software update package, or injecting malicious scripts into web pages. The manipulated communication, now containing the injected malware, is delivered to the user. The user unknowingly interacts with or downloads the compromised content. Once the user interacts with the injected content, the malicious payload is executed on their device. This can lead to various malicious activities, such as unauthorized access, data theft, or the installation of additional malware.