---
path: /http
date: 2020-06-19T19:03:05.063Z
title: HTTP allows access to information across the internet
description: A blog about HTTP
---
Before I built an HTTP server in Java, I knew little about HTTP. Hypertext Transfer Protocol is a protocol for data transfer between web browsers and servers. The client or the web browser initiates the request, and the server answers with a response. Transferred data include HTML documents, images, videos, and other content. Communication occurs via Transmission Control Protocol (TCP). The server represents a collection of servers with various functions such as sharing workload or interacting with the database. Proxies, between the browser and servers, improve performance (i.e. load balancing, caching etc.) by managing the request before sending to the server. A few characteristics of HTTP:

* **Messages**:  The browser and server transfer data through structured messages. These messages, request and response, broadly consist of a start-line, optional headers, and an optional body. The start-line includes the HTTP version, the request line includes the type of request (ex. GET, POST, DELETE, HEAD, OPTIONS etc), and the response line includes the status of the request (ex. Ok, Redirect, Method Not Allowed etc.)
* **Stateless**:  On the same connection, one request is independent of another. That is, each request contains all the information needed, and does not rely on any other request.
* **TCP Connection**:  To transfer data, the browser and server establish a connection over TCP.  With HTTP/2, multiple message request response pairs may be transferred over the same connection.
* **Caching**:  Caching involves storing a copy of a resource instead of re-fetching from the server. This strategy reduces the load on the server, and improves performance, as the resource remains closer to the client.
* **CORS**:  Cross-Origin Resource Sharing uses header information to allow a browser in a different domain than the server to communicate with each other.
* **HTTP Cookie**:  A server uses a cookie or a piece of data to identify a browser. The server sends a cookie to the browser, which it stores. The browser sends this cookie with subsequent requests. Session authentication uses a cookie to permit a user to access restricted information.

HTTP allows access to information across the internet.
