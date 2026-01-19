---
title: "Understanding the OSI Model Through a Real Example: Accessing google.com"
datePublished: Mon Jan 19 2026 04:11:10 GMT+0000 (Coordinated Universal Time)
cuid: cmkkne4mm000102l72xbu56nx
slug: understanding-the-osi-model-through-a-real-example-accessing-googlecom
tags: routing, google, networking, osi-model, tcpip-model, network-address-translation

---

Let’s understand the OSI model step by step by walking through what actually happens when we type [**https://google.com**](https://google.com) into a browser and press Enter. This explanation follows the request from the browser, through the OSI layers, hop by hop, until it reaches Google’s servers, and then back again.

---

## **Layer 7 – Application Layer**

The process begins at the **Application Layer**.

When you type [**google.com**](http://google.com) into your browser and press Enter, the browser decides it needs to fetch a web page. Since computers cannot route traffic using domain names, the browser first initiates a **DNS lookup** to resolve [google.com](http://google.com) into an IP address.

Once DNS returns the IP address of Google’s server, the browser initiates a request based on the protocol being used. Depending on the URL and configuration, this could be:

* HTTP
    
* HTTPS
    
* FTP (less common for browsers)
    

In modern browsing, this is almost always **HTTPS**. At this layer, the request is logically created — for example, an HTTP GET request — but it is not yet encrypted or transmitted.

---

## **Layer 6 – Presentation Layer**

Next, the request moves to the **Presentation Layer**.

This layer is responsible for:

* Data formatting
    
* Encryption and decryption
    
* Compression (if applicable)
    

For HTTPS traffic, encryption happens here using **TLS (Transport Layer Security)**. You do not manually encrypt anything — the browser’s TLS library handles this automatically. From this point onward, the application data (HTTP request) will be encrypted before being sent over the network.

---

## **Layer 5 – Session Layer**

The **Session Layer** is responsible for maintaining session information.

In practice, the OSI Session Layer is not cleanly separated in modern networks. What we commonly call “sessions” (such as login sessions and cookies) are handled at the **Application Layer**, while connection-oriented behavior is handled jointly by **TCP and TLS**.

At this stage:

* A logical communication session is established
    
* The connection is kept alive
    
* State is maintained so both sides understand ongoing communication
    

---

## **Layer 4 – Transport Layer**

The request now moves to the **Transport Layer**.

Here:

* The browser decides which transport protocol to use (TCP or UDP)
    
* For HTTPS over HTTP/1.1 or HTTP/2, **TCP** is used
    
* The destination port is **443**
    
* The operating system selects a random local source port
    

A **TCP connection** is established using the three-way handshake:

1. SYN from the client
    
2. SYN-ACK from Google
    
3. ACK from the client
    

Once this handshake completes, a reliable TCP connection exists between:

* Your IP address and source port
    
* Google’s IP address and port 443
    

At this layer, data is also **segmented** into smaller pieces so it can be reliably transmitted.

---

## **Back to Layer 7 – Sending the HTTP Request**

After the TCP connection and TLS handshake are complete, the browser sends the actual **HTTP request** (such as a GET request).

Although this is application-layer data, it is now:

* Encrypted by TLS
    
* Carried inside the established TCP connection
    

At this point, the request is ready to be sent across the network.

---

## **Layer 3 – Network Layer**

At the **Network Layer**, the segmented data is wrapped into **IP packets**.

Each packet contains:

* Source IP address (your device)
    
* Destination IP address (Google’s server)
    

Routers operate at this layer. They make forwarding decisions based solely on the **destination IP address**, determining the best path to route the packet toward Google.

---

## **Layer 2 – Data Link Layer**

Now the packets reach the **Data Link Layer**, where they are converted into **frames**.

Your laptop cannot send frames directly to Google, because Google is not on your local Wi-Fi network. Instead, the frame must be sent to the **next hop**, which is your **local Wi-Fi router**.

To do this, your laptop needs the router’s MAC address. This is obtained using **ARP (Address Resolution Protocol)**:

* Your laptop asks: “Who has this IP address?”
    
* The router replies with its MAC address
    

At this point:

* Destination MAC address = router’s MAC
    
* Source MAC address = your laptop’s MAC
    
* Inside the frame is the IP packet destined for Google
    

---

## **Layer 1 – Physical Layer**

At the **Physical Layer**, the frame is converted into raw bits.

These bits are transmitted as:

* Wi-Fi radio waves
    
* Electrical signals
    
* Optical signals (fiber), depending on the medium
    

The data is now physically sent out of your device.

---

## **Hop-by-Hop Behavior and NAT at the Router**

### **First Hop: Laptop → Wi-Fi Router**

The router receives the frame because the destination MAC matches its own. The router:

* Removes the Layer 2 frame
    
* Examines the Layer 3 IP packet
    

At this point, two important actions occur:

### **1\. Network Address Translation (NAT)**

Your laptop uses a **private IP address**, which cannot exist on the public internet. The router:

* Replaces your private IP and port with its public IP and a new port
    
* Stores this mapping in a NAT table so responses can be routed back to you
    

### **2\. Routing Forward**

The router forwards the packet to the ISP. For this hop:

* A new Layer 2 frame is created
    
* Destination MAC becomes the ISP-facing next-hop MAC address
    

---

## **Subsequent Hops: ISP and Internet Backbone**

As the packet travels through ISP routers and backbone routers:

* The **MAC address changes at every hop**
    
* The **destination IP (Google)** remains the same
    
* Each router repeats the same process:
    
    * Strip Layer 2
        
    * Examine Layer 3
        
    * Forward to the next hop
        

This continues until the packet reaches Google’s edge infrastructure.

---

## **Final Hop: Reaching Google**

Eventually, the packet reaches a Google server or load balancer.

At this point:

* The TCP connection is fully established end-to-end
    
* The encrypted HTTP request reaches Google
    
* Google decrypts the request using TLS
    
* Google processes the request and prepares a response
    

---

## **The Return Path (Response Back to You)**

Google sends an encrypted HTTPS response back to your public IP address.

On the return journey:

* Routers forward the packet based on IP routing
    
* Your home router receives the response
    
* NAT maps the response back to your private IP
    
* The router sends the frame to your laptop’s MAC address
    
* Your laptop decrypts the TLS data
    
* The browser parses HTML, CSS, JavaScript, and renders the page
    

You finally see the Google homepage.