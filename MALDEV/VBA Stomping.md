
VBA stomping is a technique to generate maldoc to evade AVs. This technique involves overwriting the source code of an office document with zeros or random bytes and leaving the p-code unchanged.  The source code is located in a file called VbaProject.bin which can be obtained by decompressing the document. If we open the VbaProject. bin file in a hexeditor and overwrite the bytes which represent our macro with zeros or random bytes, we can bypass detection based on VBA sourcecode. But, our macro will still run upon clicking "enable-content" because it is the p-code which is executed, not the source code.

Below is a demonstration of VBA Stomping with a non-malicious file.

![[Pasted image 20240124153410.png]]
Here, lets change the "ABC" into "XYZ".

![[Pasted image 20240124153517.png]]

Let's open the document now and check the macro content before execution.

![[Pasted image 20240124153554.png]]

Let's execute and see what happens.

![[Pasted image 20240124153615.png]]

Suddenly, "XYZ" changed to "ABC". Why? This is because even though we changed the content in the source code, it is the p-code which gets executed.

However, Note that this is an important caveat. **A VBA stomped maldoc can only be executed using the same VBA version used to create the document.**
