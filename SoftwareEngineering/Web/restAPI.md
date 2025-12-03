# RestAPIs

A Representational State Transfer (REST) is a software architecture that imposes conditions on how APIs should actually work.
RESTful API is an interface that two computers uses to exchange information securely over the internet.

> [!NOTE]
> **What is an API?**
> An Application Programming Interface (API) defines the rules that you must follow to communicate with other software systems.
> Developers expose or create APIs so that other applications can communicate with their applications programmatically.
> There are 2 main actors in APIs:
> **Client** and **Resources**
> **1.Client**
> Clients are users or software systems who wants to access data from some provider
> **2.Resources**
> Resources are information in any type (text, image, video, audio, ...) that applications provide for their clients. We call the machine that response the resources to the request as `server`. 

## What is REST?

Representational State Transfer (REST) is a software architecture that imposes conditions on how an API should work. REST was initially created as a guideline to manage communication on a complex network like the internet.
You can use REST architecture to have **high performance** and **reliable communication** at scale, and the implementation is **easy**!

> [!IMPORTANT]
> APIs that follows the REST architecture, are also knows as **REST APIs**.

REST architecture style:

### Uniform Interface

The **uniform interface** is fundamental to the design of any RESTful webservice. It indicates that the server transfers information in a standard format. The formatted resource is called a **representation** in REST. This format can be different from the internal representation of the resource on the server application.

**Uniform Interface means that all interactions between clients and servers follow a standardized way — regardless of the resource, the communication style remains consistent.**

**This simplifies the architecture because developers don’t have to learn different communication methods for each resource.**

1. Requests should identify resources. They do so by using a uniform resource identifier.
2. Clients have enough information in the resource representation to modify or delete the resource if they want to. The server meets this condition by sending metadata that describes the resource further.
3. Clients receive information about how to process the representation further. The server achieves this by sending self-descriptive messages that contain metadata about how the client can best use them.
4. Clients receive information about all other related resources they need to complete a task. The server achieves this by sending hyperlinks in the representation so that clients can dynamically discover more resources.

### Client-Server Based

The uniform interface separates user concerns from data storage concerns. The client’s domain concerns UI and request-gathering, while the server’s domain concerns focus on data access, workload management, and security. The separation of client and server enables each to be developed and enhanced independently of the other.

### Statelessness

In REST architecture, statelessness refers to a communication method in which the server completes every client request independently of all previous requests.
Request from client to server must contain all of the information necessary so that the server can understand and process it accordingly. The server can't hold any information about the client state.

### Layered system

In a layered system architecture, the client can connect to other authorized intermediaries between the client and server, and it will still receive responses from the server. Servers can also pass on requests to other servers. You can design your RESTful web service to run on several servers with multiple layers such as security, application, and business logic, working together to fulfill client requests. These layers remain invisible to the client.

**REST allows for an architecture composed of hierarchical layers. In doing so, each component cannot see beyond the immediate layer with which they are interacting.**

### RESTful Resource Caching

RESTful web services support caching, which is the process of storing some responses on the client or on an intermediary to improve server response time. For example, suppose that you visit a website that has common header and footer images on every page. Every time you visit a new website page, the server must resend the same images. To avoid this, the client caches or stores these images after the first response and then uses the images directly from the cache. RESTful web services control caching by using API responses that define themselves as **cacheable** or **noncacheable**.

### Code On Demand

In REST architectural style, servers can temporarily extend or customize client functionality by transferring software programming code to the client. For example, when you fill a registration form on any website, your browser immediately highlights any mistakes you make, such as incorrect phone numbers. It can do this because of the code sent by the server.

## Benefits of using RES APIs

1. Scalability
2. Flexibility
3. Independence

### Scalability

Systems that implements REST APIs can scale efficiently because REST optimizes client-server interactions.
Statelessness removes server load because server doesn't need to fetch previous request payload and information.
Well-managed caching partially or completely eliminates some client-server interactions. All these features support scalability without causing communication bottlenecks that reduce performance.

### Flexibility

RESTful web services support total client-server separation. They simplify and decouple various server components so that each part can evolve independently. Platform or technology changes at the server application do not affect the client application. The ability to layer application functions increases flexibility even further. For example, developers can make changes to the database layer without rewriting the application logic.

### Independency

REST APIs are independent of the technology used. You can write both client and server applications in various programming languages without affecting the API design. You can also change the underlying technology on either side without affecting the communication.

### How do RESTful APIs work?

The basic function of a RESTful API is the same as browsing the internet. The client contacts the server by using the API when it requires a resource. API developers explain how the client should use the REST API in the server application API documentation.

These are the general steps for any REST API call:

1. The client sends a request to the server. The client follows the API documentation to format the request in a way that the server understands.
2. The server authenticates the client and confirms that the client has the right to make that request.
3. The server receives the request and processes it internally.
4. The server returns a response to the client. The response contains information that tells the client whether the request was successful. The response also includes any information that the client requested.

## What does the client request contains?

### 1.Unique resource identifier

The server identifies each resource with unique resource identifiers. For REST services, the server typically performs resource identification by using a Uniform Resource Locator (URL). The URL specifies the path to the resource. A URL is similar to the website address that you enter into your browser to visit any webpage. The URL is also called the request endpoint and clearly specifies to the server what the client requires.

### 2.Method

Developers often implement RESTful APIs by using the Hypertext Transfer Protocol (HTTP). An HTTP method tells the server what it needs to do to the resource. The following are four common HTTP methods:

#### 2.1 GET

Clients use `GET` to access resources that are located at the specified URL on the server. They can cache GET requests and send parameters in the RESTful API request to instruct the server to filter data before sending.

#### 2.2 POST

Clients uses `POST` to send data to the server.

#### 2.3 PUT

Client use `PUT` to update existing data and resources on the server.

#### 2.4 DELETE

Client uses the `DELETE` request to remove the resources.

### 3.HTTP Headers

Request headers are the **metadata exchanged between the client and server**. For instance, the request header indicates the `format of the request and response`, provides information about request status, and so on.

### 4.Data

REST API requests might include data for the POST, PUT, and other HTTP methods to work successfully.

### 5.Parameters

RESTful API requests can include parameters that give the server more details about what needs to be done. The following are some different types of parameters:

- Path parameters that specify URL details.
- Query parameters that request more information about the resource.
- Cookie parameters that authenticate clients quickly.

## What are RESTful API authentication methods?

A RESTful web service must authenticate requests before it can send a response. Authentication is the process of verifying an identity

Common RESTful API authentication methods:

### 1.HTTP authentication

HTTP has different types of authentications:

#### 1.1 Basic Authentication

In basic authentication, the client sends the user name and password in the request header. It encodes them with base64, which is an encoding technique that converts the pair into a set of 64 characters for safe transmission.

> [!NOTE]
> In basic authentication, you have to combine your username and password, and then encode it using base-64 encoding algorithm and set it in `Authorization` Header using `basic` identifier.
> `Authorization: Basic <base64(username:password)>`
> Example:
> `Authorization: Basic bXl1c2VyOm15cGFzc3dvcmQ=`
> I have combined `myusername` which is my username and `mypassword` which is my password, and I get this: `myusername:mypassword`
> Then I encode it using base64 and I get this string: `bXl1c2VyOm15cGFzc3dvcmQ=`

- *[Source*](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Authentication)

#### 1.2 Bearer Authentication

The term bearer authentication refers to the process of giving access control to the token bearer. The bearer token is typically an encrypted string of characters that the server generates in response to a login request. The client sends the token in the request headers to access resources.

### 2. API Keys

API keys are another option for REST API authentication. In this approach, the server assigns a unique generated value to a first-time client. Whenever the client tries to access resources, it uses the unique API key to verify itself. API keys are less secure because the client has to transmit the key, which makes it vulnerable to network theft.

### 3. OAuth

OAuth combines passwords and tokens for highly secure login access to any system. The server first requests a password and then asks for an additional token to complete the authorization process. It can check the token at any time and also over time with a specific scope and longevity.

## What does the RESTful API server response contain?

### Status line (Status code)

The status line contains a three-digit status code that communicates request success or failure. For instance, 2XX codes indicate success, but 4XX and 5XX codes indicate errors. 3XX codes indicate URL redirection.

Some common codes:

1. `200`: Generic success response
2. `201`: POST method success response
3. `400`: Incorrect request that the server cannot process
4. `404`: Resource not found

### Message Body

The response body contains the resource representation. The server selects an appropriate representation format based on what the request headers contain. Clients can request information in XML or JSON formats, which define how the data is written in plain text. For example, if the client requests the name and age of a person named John, the server returns a JSON representation as follows:

```js
'{"name":"John", "age":30}'
```

### Headers

The response also contains headers or metadata about the response. They give more context about the response and include information such as the server, encoding, date, and content type.

#### Sources

1. [AWS - What is a RESTful API?](https://aws.amazon.com/what-is/restful-api/)
2. [Postman - What is a RESTful API?](https://blog.postman.com/rest-api-examples/)
3. [Postman - different type of APIs](https://blog.postman.com/different-types-of-apis/)
4. [TechTarget - What is RESTful API?](https://www.techtarget.com/searchapparchitecture/definition/RESTful-API)
5. [Microsoft - Design a RESTful API](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)
