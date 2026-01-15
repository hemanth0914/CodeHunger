---
title: "How Browsers Load a Website: From URL to Page Display"
datePublished: Thu Jul 24 2025 19:45:36 GMT+0000 (Coordinated Universal Time)
cuid: cmdhsybkn000g02kz3nyn0nr2
slug: how-browsers-load-a-website-from-url-to-page-display
tags: browsers, system-design, url

---

Ever wondered what happens when you enter a URL on the browser and hit ENTER. What’s happening in the background. How are we able to see the webpage with rich contents.

What Really Happens When You Type a URL and Press Enter?

A common answer is: “The browser talks to the server.”

While not incorrect, this explanation is shallow.

In reality, when a user enters a URL, they are requesting a resource, and that request passes through multiple specialized systems, each responsible for a specific task. Modern internet architectures—especially those using AWS, Cloudflare, or similar cloud platforms—are built as layered systems designed for scalability, security, and reliability.

Let’s walk through the process step by step.

⸻

**Step 1: DNS Resolution – Finding the Address**

Computers do not understand domain names like [google.com](http://google.com) or [facebook.com](http://facebook.com). They communicate using IP addresses.

When a user enters a URL such as [https://facebook.com](https://facebook.com):

1\. The browser first asks the operating system if it already knows the IP address (via local cache).

2\. If not, the OS queries a DNS resolver (commonly Google DNS at 8.8.8.8).

3\. The resolver follows the DNS hierarchy:

• Root servers direct the request to the correct TLD server.

• TLD servers (e.g., .com) identify the authoritative server.

• Authoritative DNS servers return the final IP address.

*Why CNAME Flattening Matters*

Sometimes a domain does not point directly to an IP address. Instead, it points to another domain using a CNAME record. Without flattening, DNS resolution would require multiple lookups. CNAME flattening resolves these indirections in advance and returns a single final IP address, reducing latency and improving performance.

⸻

**Step 2: TCP and TLS Handshake – Establishing Trust**

Before any data is sent, a secure and reliable connection must be established.

**TCP Handshake**

The browser and server perform a three-way handshake:

• SYN (client asks to connect)

• SYN-ACK (server agrees)

• ACK (client confirms)

This establishes a reliable communication channel.

*TLS Handshake*

TLS ensures secure communication by:

• Verifying the server’s identity

• Agreeing on encryption algorithms

• Exchanging encryption keys

In modern systems, this handshake typically happens at the CDN or Load Balancer, not at the application server.

⸻

**Step 3: CDN – The Internet Edge**

The request usually reaches a Content Delivery Network (CDN) first.

*The CDN:*

• Terminates TLS (in most architectures)

• Performs WAF checks (SQL injection, XSS, bot detection)

• Applies rate limiting and DDoS protection

*Caching*

If the request is for a static asset (images, CSS, JavaScript), the CDN serves it immediately.

If the request is for dynamic data (APIs), it is forwarded downstream.

⸻

**Step 4: Load Balancer – Traffic Control**

After the CDN, the request reaches the Load Balancer inside a private cloud network (VPC).

The Load Balancer:

• Decrypts traffic if TLS termination happens here

• Inspects headers, paths, and hostnames

• Determines which backend service should handle the request

• Routes traffic only to healthy instances

This layer enables horizontal scaling and fault tolerance.

⸻

**Step 5: Kubernetes – Running the Application**

Behind the Load Balancer is typically a Kubernetes cluster.

Traffic flows as follows:

• Ingress Controller routes the request based on path or hostname

• Service selects a healthy Pod

• Pod runs the application code

Pods are ephemeral and disposable; Kubernetes continuously replaces failed instances to maintain availability.

⸻

**Step 6: Database Access – Retrieving State**

Applications are generally stateless, so persistent data is stored in databases.

The application:

• Retrieves a connection from a connection pool

• Executes queries against a primary or replica database

• Returns the result to the application logic

Connection pooling avoids the overhead of creating new database connections for every request.

⸻

**Step 7: The Return Path – Sending the Response**

The response travels back through the same layers:

• Application → Service → Ingress → Load Balancer

• Load Balancer may compress the response

• CDN may cache it

• Data is encrypted and sent to the browser

The browser then decrypts the response, interprets the returned data, and renders the resulting HTML or content for display to the user.

⸻

Key Takeaway

When a user presses Enter, they are not talking to a single server. They are interacting with an ecosystem of systems:

• DNS infrastructure

• Encryption protocols

• CDNs and WAFs

• Load balancers

• Kubernetes clusters

• Databases

Each layer exists because systems at internet scale must assume failure, latency, and malicious traffic. Modern architectures trade simplicity for resilience, security, and performance.

Understanding this flow demonstrates strong system design fundamentals and is a cornerstone of backend and infrastructure engineering.