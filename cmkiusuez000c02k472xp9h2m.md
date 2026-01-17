---
title: "Why Debugging Microservices Feels Like a Murder Mystery"
datePublished: Sat Jan 17 2026 22:03:02 GMT+0000 (Coordinated Universal Time)
cuid: cmkiusuez000c02k472xp9h2m
slug: why-debugging-microservices-feels-like-a-murder-mystery
tags: microservices, debugging, logging, distributed-tracing, tracing, opentelemetry

---

Debugging in a monolithic system is usually straightforward. All the code runs inside a single application, so one request maps to one process. When something goes wrong, you typically get a stack trace that clearly tells you which function failed and on which line. You can trace the error directly through the call stack, understand how the failure happened, and fix it quickly.

In a monolith, everything happens in one place. Logs, code, and execution context are all centralized, which makes debugging relatively simple and predictable.

---

*Microservices are very different.*

In a microservices architecture, a single feature is spread across many independent services. Each service runs separately, often on different machines or containers, and each one has its own logs.

Consider a simple example:

* Service A calls Service B
    
* Service B calls Service C
    
* Service C calls Service D
    
* Service D crashes
    

What does Service A return to the user?

A generic **500 Internal Server Error**.

Now you start debugging:

* You check Service A’s logs — nothing useful
    
* You check Service B’s logs — everything looks fine
    
* You check Service C’s logs — still fine
    

You may not even know that Service D was involved at all.

This is why debugging microservices feels like solving a murder mystery. You know something failed, but you don’t know where, why, or how the failure propagated through the system.

---

## **Why Logs Alone Are Not Enough**

Logs answer one narrow question:

> “What happened inside this service?”

But logs do **not** tell you:

* Which service initiated the request
    
* Which downstream services were involved
    
* Which exact request triggered the failure
    

Each service logs its own view of the world, but there is no built-in way to connect those logs across services.

To debug microservices effectively, we must **connect the dots across the entire system**.

---

## **The Core Idea: Distributed Tracing**

Distributed tracing answers one simple but powerful question:

> “What path did this request take through the system?”

Instead of guessing which services were involved, we track the request end-to-end as it flows through multiple services.

---

## **A Real-World Example: Placing an Order**

Imagine placing an order in a food delivery app.

That single click may involve:

* User Service (authentication)
    
* Restaurant Service (availability)
    
* Payment Service (charging the card)
    
* Driver Service (assignment)
    
* Notification Service (status updates)
    

This is one user action, but many services work together behind the scenes. Distributed tracing lets us see:

* Which services were called
    
* In what order
    
* How long each service took
    
* Where the request failed or slowed down
    

---

## **How Distributed Tracing Works**

Think of tracing like tracking a package through a delivery system.

---

### **Trace ID: Identifying the Request**

When a request first enters the system, usually at the API Gateway, a **unique Trace ID** is generated. This Trace ID is attached to the request as a header.

Every service that handles the request:

* Reads the Trace ID
    
* Logs it
    
* Passes it along to the next service
    

Now all services know:

> “The work I am doing belongs to this specific request.”

---

### **Spans: Tracking Individual Work Units**

A **Span** represents a single unit of work, such as:

* Processing a payment
    
* Calling an external API
    
* Sending a notification
    

Each span records:

* Start time
    
* End time
    
* Parent span (which operation triggered it)
    

These spans form a tree that shows the full execution flow. This makes it easy to see:

* Which services were called
    
* How long each operation took
    
* Where delays or failures occurred
    

---

## **Tracing Tools**

Tracing systems such as OpenTelemetry, Jaeger, and Zipkin:

* Collect trace data from services
    
* Store it efficiently
    
* Display it visually as timelines
    

With these tools, you can click on a single request and immediately see:

* All involved services
    
* The execution order
    
* Performance bottlenecks
    
* The exact failure point
    

This replaces guesswork with visibility.

---

## **Why We Don’t Trace Every Request**

In high-scale systems, millions of requests can occur every minute. Tracing every single request would generate massive amounts of data, leading to high storage and network costs.

To manage this, systems use **sampling**.

---

## **Sampling Strategies**

### **Head-Based Sampling**

The decision to trace is made at the entry point. For example, the system might trace only 1% of requests.

This approach is:

* Simple
    
* Cheap
    
* Easy to implement
    

However, it can miss important failures if they occur in non-sampled requests.

---

### **Tail-Based Sampling**

In tail-based sampling, all requests are traced temporarily in memory. Only traces that matter — such as errors or slow requests — are stored permanently.

This approach:

* Captures real problems
    
* Is far more useful for debugging
    
* Is harder to implement
    
* Uses more memory
    

---

## **Logs vs Metrics vs Traces**

Each observability tool answers a different question.

Logs tell you **what happened**.

Metrics tell you **when it happened and how often**.

Traces tell you **where it happened and why**.

For example:

* Logs might say “Payment failed due to timeout”
    
* Metrics show a spike in error rate or latency
    
* Traces reveal that the timeout occurred during a downstream bank API call
    

In microservices, you need all three to understand the system fully.

---

## **The Key Takeaway**

Distributed tracing tracks a single request across multiple services using a shared Trace ID. It allows engineers to see the full journey of a request, making it possible to identify failures, delays, and bottlenecks in complex microservices systems.

In short:

* Logs provide details
    
* Metrics provide trends
    
* Traces provide the story