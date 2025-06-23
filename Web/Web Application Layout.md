

Web Application layouts can consist of many different layers :



| Category                       | Description                                                                                                                                                                                                                                                |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Web Application Infrastructure | Describes the structure of required components, such as the database, needed for the web application to function as intended, Since these, web applications can be setup to run on a separate server, it is essential to know which DB it needs to access. |
| Web Application components     | The components that make up a web application represent all the components that the web application interacts with.                                                                                                                                        |
| Web Application Architecture   | Architecture Comprises all the relationships between the various WebApp components.                                                                                                                                                                        |




### Web Application Infrastructure



- Web applications can use many different Infrastructure setups, called models
- Most Common Models are :
	- **Client-Server** : In a client-server model, A server hosts a webapp and distributes it to any client trying to access it. 
	- **One Server** : In this model, the entire webapp or even several webapps and their components, including DBs are hosted on a single server. 
	- **Many Servers - One Database** : This model separates the DB onto its own DB server and allows the webapps' hosting server to access the DB server to store and retrieve data. 
	- **Many Servers  -  Many Database** : This model builds up on the previous model, but each webapp's data is hosted in a separate DB. 






### Web Application Components


Each Web Application can have different number of components. Nevertheless, all of the components of the models mentioned previously can be broken down to : 

1. Client
2. Server
	1. Database
	2. WebApp Logic
	3. Webserver

3. Services
	1. 3rd Party Integrations
	2. WebApp Integrations

4. Functions







### WebApp Architecture


The components of a WebApp are divided into three different layers 


| Layer              | Description                                                                                                                                         |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Presentation Layer | - Consists of the UI process components.<br>- Can be accessed by the client via the web browser and<br>are returned in the form of HTML, CSS and JS |
| Application Layer  | - This layer ensures that all client requests are correctly processed.<br>- Various                                                                 |
