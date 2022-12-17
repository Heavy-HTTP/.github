# Heavy HTTP

With Rest and HTTP, the request-response pattern has almost concerned the Client-Server communication.  Even though the combination of Rest and HTTP provides an illusion of a universal methodology for communication there are many practical limitations in this approach. By introducing Heavy HTTP, we solve one of the key practical limitations of this communication pattern and extends its capabilities. 

The size of the payload of requests/responses is one of the most significant practical limitations in this pattern. With the latest trends, modern web applications more often exchange large amounts of data back and forth with HTTP and Rest. But with a single HTTP request/response, the process is highly inefficient. Not just that most of the server implementations can't handle large requests/responses. As an example, these are the payload size limits of the Request/Response handling interfaces of the world's largest cloud service providers. 

| Service Provider        | Type           | Threshold  |
| ------------- |:-------------:| -----:|
| AWS      | Lambda as ELB Targets | 1MB |
| AWS      | API Gateway      |   10MB |
| GCP | API Gateway      |    32MB |

This payload size limitation literally ties our hands because, 

1. You never know when the payload will go beyond the threshold 
	In web applications, it's hard to predict the client inputs. They may send a little amount of information or a very big chunk of data. Same as in the server end. With complex relationship models, even with pagination, the payload size can vary in a vast range. That means either some of the client inputs or server responses will be ignored (crashed!) because no one can predict the size of the data. 

2. Implementation of alternative methods requires a lot of effort
	Providing an alternative communication would require additional boilerplate code and logic on both client and server ends. And the problem becomes much worse when it is required to measure the payload size depending on payload type.
  
When you deal with large payload sizes the next inherent issue is connection time limitations. When the request is heavy it most likely takes a longer time to process and that could lead to connection timeouts. Wouldn't it be cool if it's possible to handle heavy requests asynchronously while handling other requests synchronously? 

Well **Heavy HTTP** is there to save you from all the trouble. **Heavy HTTP is a utility framework that automatically handles the payload limitations in client-server Rest HTTP communication with very minimal configurations**. It utilizes signed URLs as an alternative communication pattern to overcome the payload size limitation and provides transporters to control the request data in the server side. Most importantly, it doesn't interrupt the existing communication patterns. So only by performing very few modifications, any existing application can be retrofitted to work with Heavy HTTP. 

Heavy HTTP consists of three main components 

1. Heavy HTTP Client Connector
2. Heavy HTTP Server Connector
3. Heavy HTTP Transporter


### Heavy HTTP Client Connector
Heavy HTTP Client is an extension of the default HTTP Client that is used in the application.  If the application is a browser, Heavy HTTP Client extends the XMLHttpRequest and Fetch API* and if the application is written in Node JS then Heavy HTTP Client extends the Node HTTP Client*. For all the HTTP communication the extended Heavy HTTP Client is exposed via the same interface. Because of this pattern, regardless of the HTTP client wrapper library (Axios, Node Fetch etc) that is used in the application, Heavy HTTP can perform its magic. 

#### Looking under the hood 
At the initialization of the request Heavy HTTP Client performs the following operations
1. Identify the type of the payload and estimate the size of the payload.
2. If the size of the payload is beyond the configured threshold shift to the Heavy Http Transporter to continue the communication. If not proceed with the existing communication pattern. 
3. Provide the seamless experience of HTTP client to the HTTP Client wrapper library. 

When receiving the response Heavy HTTP Client performs the following operations
1. Check whether the response is a Heavy Response or not. 
2. If it's a Heavy Response then shift to the Heavy Http Transporter to fetch the data. Otherwise, proceed with the existing communication pattern. 
3. Provide the seamless experience of HTTP client to the HTTP Client wrapper library. 

### Heavy HTTP Server Connector
This is a runtime-specific library which is responsible for the server end of communication with the Heavy HTTP Client that makes the request. Since the library is runtime-specific it needs to be attached to the runtime as an HTTP interceptor. Since the library is attached as an interceptor whatever the APIs provided by the runtime can be used without an issue with the library. Similar to the Heavy HTTP Client, Heavy HTTP Server also can perform its magic regardless of which API is used for the communication. 

#### Looking under the hood 
When receiving the request from HTTP Client, Heavy HTTP Server performs the following operations
1. Identify whether the request is a Heavy Request or not (Based on request headers). 
2. If it's a Heavy Request shift to the Heavy Http Transporter to fetch the request data. Otherwise, proceed with the existing communication pattern. 
3. Provide the seamless experience of the HTTP request to the runtime APIs. 

When sending the response to HTTP Client, Heavy HTTP Server performs the following operations
1. Identify the type of the payload and estimate the size of the payload.
2. If the size of the payload is beyond the configured threshold shift to the Heavy Http Transporter to continue the communication. If not proceed with the existing communication pattern. 
3. Provide the seamless experience of HTTP response to the HTTP Client. (If the response is a heavy response, then the HTTP Client must be a Heavy HTTP Client to understand the protocol). 

### Heavy HTTP Transporter
Any temporary/permanent storage mechanism that provides signed URLs for upload and download purposes can be a transporter. The transporter is attached to the Heavy HTTP Server so that it is fully decoupled from the Heavy HTTP client. Multiple transporters are already created for the popular storages in the sdsdsd repo and they are ready to go. But if you would like to create your own transporter you can do it by just following the sdsddsds section in Heavy HTTP Server. This gives you the control of handling the request and response as you like. If you want to process the request in lazy manner (given that request is heavy) that also can be acheived as well. For further information please refer adasdasdasd

### Heavy HTTP Communication Protocol 
![alt text](https://github.com/Heavy-HTTP/.github/blob/main/profile/Heavy-HTTP-Communication-Protocol.png?raw=true)

* The flow from 1 - 11 only gets triggers if the request body is size greater than the request content threshold. Otherwise, the original request would flow to the server without any changes.
* The flow from 12 - 20 only gets triggers if the response body is size greater than the response content threshold. Otherwise, the original response would flow to the client without any changes.
* The flow from 8 - 11 is not necessarily to be the same as it is mentioned in the protocol. The developer has the freedom to modify that particular flow. For further information please refer adasdasdasd

### Heavy HTTP Implementation
