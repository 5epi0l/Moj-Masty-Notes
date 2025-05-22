
NTA can be described as the act of examining network traffic to characterize common ports and protocols utilized, established a baseline for our environment, monitor and respond to threats




###  Cores of NTA

1. Collecting
2. Setting 
3. Identifying
4. Detecting




### BPF Syntax

BPF syntax is a network tap that allows for monitoring and filtering packets at the kernel level. 

 1. A tiny, sandboxed program that runs safely inside the Linux Kernel
 2. It provides filtering and decoding abilities






### NTA Workflow

1. **Ingest Traffic** : Once we have decided on our placement, begin capture traffic. Utilize capture filters if we already have an idea of what we are looking for.
2. **Reduce Noise** :  Once we complete the initial capture, we need to filter out the unnecessary traffic from our view can make analysis easier.
3. **Analyze And Explore**: Now it is time to start carving out data pertinent to the issue we are chasing down. For eg, looking at specific ports , hosts, protocols, even things as specific as flags set in the TCP header. 
4. **Detect and Alert** : Are we seeing any errors? Is a device not responding that should be? Use our analysis to decide if what we see is benign or potentially malicious







### TCP SESSION TEARDOWN


#### FIN Flag:

The FIN flag is used for signalling that the data transfer is finished and the sender is requesting to terminate the connection


1. The Client acknowledges the receipt of the data and then sends a FIN and ACK to begin session termination
2. The server responds with an acknowledgement of the FIN and sends back its own FIN.
3. Finally the client acknowledges the session is complete and closes the connection.
