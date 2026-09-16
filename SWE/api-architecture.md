# API Architecture
An Application Programming Interface (API) is a set of rules and protocols that allows different software applications to communicate with each other. It defines the methods and data formats that applications can use to request and exchange information.
There are three major API architectural styles: REST, RPC, and GraphQL

## REST: Representational State Transfer
REST is an architectural style that uses a stateless, client-server communication model. It's the most widely used approach for designing web services. The core idea of REST is to treat all server-side data as resources that can be created, read, updated, or deleted (CRUD) using standard HTTP methods.

### Core Principles of REST
- **Client-Server Architecture** - The client and server are separater entities that communicate over a network.
- **Statelessness** - Each request from a client to a server must contain all the information needed to understand and process the request (URL + method + headers + body + auth). The server does not store any client context between requests. Ex: In session based auth server stores the session id. (Stateful) whereas in token based auth server processes request with the token without storing it. (Stateless). 
- **Cacheability** - Responses must, implicitly or explicitly, define themselves as cacheable or not, to prevent clients from reusing stale or inappropriate data in subsequent requests.
    - **Cache-Control** - Directives that control caching behavior.
    `Cache-Control: no-store` - Do not cache at all.
    `Cache-Control: no-cache` - Must revalidate before reusing.
    `Cache-Control: public, max-age=3600` - Cacheable by browsers & proxies for 1 hour.
    `Cache-Control: private, max-age=600` - Cacheable only by the end-user’s browser for 10 min.
- **Uniform Interface** - This is a key constraint and is defined by four principles:
    - **Resource Identification in Requests** - Resources are identified using a Uniform Resource Identifier (URI), such as /users/123.
    - **Resource Manipulation Through Representations** - The client receives a representation of the resource (commonly in JSON or XML format) and can modify the resource by sending this representation back to the server.
    - **Self-descriptive Messages** - Each message includes enough information to describe how to process it.
    - **Hypermedia as the Engine of Application State (HATEOAS)** - Responses from the server should contain links to other related resources, allowing the client to navigate the API by following these links.

### When to use REST:
- For public APIs that need to be easily understood and consumed by a wide range of clients.
- When you need to take advantage of HTTP caching.
- For simple, resource-oriented applications.

## RPC: Remote Procedure Call
RPC is a protocol that one program can use to request a service from a program located in another computer on a network without having to understand the network's details.

### How RPC works
- The client calls a client stub. The call is a local procedure call, with parameters pushed onto the stack as usual.
- The client stub packs the parameters into a message (a process called marshalling).
- The client's local operating system sends the message from the client machine to the server machine.
- The server's local operating system passes the incoming packets to the server stub.
- The server stub unpacks the parameters (unmarshalling).
- Finally, the server stub calls the server procedure. The reply traces the same steps in the reverse direction.

### When to use RPC
- For internal communication between microservices where performance is critical.
- When you need to execute specific actions on a remote server.
- For applications that require streaming data.

## GraphQL: A Query Language for APIs
GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data. It was developed to address the limitations of REST, such as over-fetching (getting more data than you need) and under-fetching (not getting enough data in a single request, requiring multiple API calls).

### Key Concepts of GraphQL
- **Schema** - A GraphQL API is defined by a schema, which is a strongly typed description of the data that can be queried.
- **Queries** - Clients request data from a GraphQL API by sending queries. The structure of the query mirrors the structure of the data that will be returned.
- **Mutations** - To modify data (create, update, or delete), clients send mutations.
- **Subscriptions** - For real-time updates, clients can use subscriptions to receive data from the server as it becomes available.

### When to use GraphQL
- For applications with complex data requirements and a need for flexible data fetching.
- When you need to aggregate data from multiple sources.
- For mobile applications where minimizing network requests is important.

## WebSocket
A WebSocket is a persistent, full-duplex communication channel over a single TCP connection. It starts as an HTTP request, then upgrades to WebSocket protocol. Unlike normal HTTP, which is request-response, WebSockets allow both client and server to send messages anytime.

### When to Use
- You need real-time, bidirectional communication.
- Continuous updates are critical (stock prices, game states, chat apps).
- Millisecond-level responsiveness matters.

## Webhook
A Webhook is a way for one system to notify another system via an HTTP request when an event happens. It’s essentially a reverse API call: instead of your app asking every few seconds, the external service pushes data to your endpoint automatically.

### When to Use
- You need event notifications but don’t want to hold an open connection.
- Your app doesn’t require millisecond updates.
