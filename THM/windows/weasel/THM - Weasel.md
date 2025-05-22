5epi0l | 23 Jan | 2024


### Notes:

- Open Ports: 22,8888,139,135,445,3389

- Doesn't look like a DC.

- SMB signing is disabled, maybe susceptible to NTLM relay.

- HOST Name: DEV-DATASCI-JUP

- NULL SESSION is enabled.

- jupyter version 6.0.3, could be vuln to RCE

-  Users on the box:
	![[Pasted image 20240123203941.png]]

- Found shares
	- data-sci team is readable and writable
	- IPC$ is readable

- Found some usernames from a pdf, using username-anarchy to construct a list of possible usernames

- 