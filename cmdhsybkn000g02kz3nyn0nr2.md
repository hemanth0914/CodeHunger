---
title: "How Browsers Load a Website: From URL to Page Display"
datePublished: Thu Jul 24 2025 19:45:36 GMT+0000 (Coordinated Universal Time)
cuid: cmdhsybkn000g02kz3nyn0nr2
slug: how-browsers-load-a-website-from-url-to-page-display
tags: browsers, url

---

Ever wondered what happens when you enter a URL on the browser and hit ENTER. What’s happening in the background. How are we able to see the webpage with rich contents.  
  
Let me explain step by step process how this happens.

1. *URL Parsing:*
    
    *You type* [*https://codehunger.hashnode.dev/seamless-streaming-the-magic-of-adaptive-bitrate-technology*](https://codehunger.hashnode.dev/seamless-streaming-the-magic-of-adaptive-bitrate-technology) *in the browser*
    
    *In this URL,*
    
    * *Scheme:* `https://`
        
        *HTTPS stands for Hypertext Transfer Protocol Secure. This tells the browser to make a connection to the server using TLS(Transport Layer Security). TLS is encryption protocol to secure communication over the Internet. The data exchanged in between the browser and the server will be encrypted.*
        
    
    * **Domain:** `codehunger.hashnode.dev`
        
        This is the domain name. It points to the specific IP address of the server.
        
    * **Path:** `seamless-streaming-the-magic-of-adaptive-bitrate-technology`
        
        This is the path on the server to the requested resource.
        
2. Browser looks up IP Address for the domain(DNS lookup)
    
    Browser need to find the IP address of the server hosting the website using the domain. First browser checks it’s own cache, the OS cache, a local network cache at our router, a DNS Server cache at our ISP. If browser still can’t find the IP Address, the DNS server at the ISP does a recursive DNS lookup until it is found.
    
3. TCP Connection with server
    
    Packets are sent from client browser through the router, ISP, through an internet exchange of ISP’s using TCP(Tranmission Control Protocol) to find the server with the IP Address to connect to. But this is not efficient
    
    Instead many websites use CDN to cache static and dynamic content closer to the browser.  
    The CDN is a geographically distributed network of servers that cache and serve content closer to the user. So when CDN comes into play, the domain points to a CDN Endpoint. DNS returns IP Address of a nearby CDN Edge server based on user’s location. CDN uses <mark>Any Cast routing</mark> to direct the user to closest server geographically for faster access
    
    Once the browser finds the server, it establishes a TCP connection with the server and if HTTPS is being used, a TLS handshake takes place to secure the communication.
    
    * Browser and server agree on encryption keys and algorithms.
        
    * Server sends its SSL certificate for verification.
        
    * A secure encrypted connection is established.
        
4. Browser sends a HTTP request
    
    The request line looks like this
    
    `GET /seamless-streaming-the-magic-of-adaptive-bitrate-technology HTTP 1.1`
    
    It contains the following
    
    * A request method: GET, POST, PUT, etc.
        
    * The path, pointing to requested resources
        
    * HTTP version
        
5. Server processes the request
    
    Server received the request and then runs the backend logic to generate a response
    
    Usually
    
    * A status line
        
    * Response headers, telling the browser how to handle the response
        
    * Requested resources, either HTML, JS, CSS, image files, or data
        
6. Browser renders the content
    
    Once browser has received the response, it inspects the headers on how to render the resource. The `Content-type` header tells the browser what type of resource it received. Let’s say HTML, the browser knows what to do with HTML. So it parses the HTML and it makes additional requests to get JS, CSS, images and Data. The DOM is built, CSS is applied and JavaScript is executed. Finally page will be displayed.
    
    ### Without CDN
    
    `URL → DNS → TCP → TLS → HTTP Request → Server → HTTP Response → HTML → Render`
    
    ### With CDN
    
    `User --> DNS --> CDN Edge (cache hit?) --> [maybe] Origin --> Response --> Browser`