

#### PrivEsc

if tomcat is running JDWP (Java Debug Wire Protocol), it is possible to achieve RCE.  

We can check if tomcat is running with JDWP by looking for :

```bash
-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005
```

JDWP is used to remotely debug Java Applications.  JDWP allows us to access and Invoke objects residing in memory.  It provides built-in commands to load arbitrary classes into the JVM memory and invoke already existing or newly loaded bytecode. 

This behaviour allows for arbitrary code execution within the JVM memory space. 



#### Exploitation

We can use [https://IOActive/jdwp-shellifier] for exploiting JDWP.


