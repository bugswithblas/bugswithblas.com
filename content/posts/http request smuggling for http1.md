---
title: "HTTP Request Smuggling for HTTP/1.1"
description: ""
showTableOfContents: true
tags:
  - "Request Smuggling"
  - HTTP
type: post
draft: true
date: 2025-02-16
---


## 1. Introduction

As I was preparing to pass the BSCP exam, the most challenging and unfamiliar type of vulnerability for me was HTTP request smuggling.

To fully understand this attack, it is essential to grasp the fundamentals of the HTTP protocol and how modern web applications are structured, particularly regarding proxy servers, load balancers, and back-end architecture. In this article, I will expand my knowledge by exploring every aspect of this vulnerability.

For someone unfamiliar with this attack, request smuggling exploits discrepancies in how HTTP requests are parsed between front-end and back-end servers. The goal is to manipulate request boundaries, often causing one request to be interpreted as two separate ones. How is this accomplished? Why does this happen? These are the questions I will attempt to answer."

The impact of request smuggling varies depending on the web application's infrastructure and security controls. In some cases, it can allow an attacker to bypass authentication, access restricted resources, or hijack user sessions—posing a serious security risk.



## 2. Understanding Core HTTP Headers in Request Smuggling

Before diving into attack techniques, it’s important to understand the headers that play a crucial role in HTTP request parsing.  

But first, when were these headers introduced, and what problems did they solve? [HTTP/1.1](https://developer.mozilla.org/en-US/docs/Web/HTTP/Evolution_of_HTTP#http1.1_%E2%80%93_the_standardized_protocol) was introduced in 1997 to address some of the issues in version 1.0. The main problem with HTTP/1.0 was its inefficiency—each request required a full **3-way handshake** before it could be processed. Additionally, it lacked built-in support for persistent connections and caching mechanisms. One of the solutions introduced in HTTP/1.1 was the Transfer-Encoding header, which helped improve efficiency in data transmission.

### **Key HTTP Headers Relevant to Request Smuggling**

#### **Connection and Keep-Alive**  
While the Content-Length and Transfer-Encoding headers specify message lengths, the Connection and Keep-Alive headers allow a connection to remain open for multiple requests, reducing the need for a **3-way handshake** with every request.  

The Connection header has two primary options:
- **keep-alive** (default in HTTP/1.1): Keeps the connection open for subsequent requests.
- **close**: Closes the connection after the response is sent.  

The Keep-Alive header specifies the number of requests allowed before the connection is closed and the duration for which an idle connection remains open.  
Example: 

```bash
Keep-Alive: timeout=5, max=200
```

This means the connection will remain open for up to 5 seconds and allow up to 200 requests before closing.


#### **Content-Length**  
What is the format of Content-Length, and why is it used?  

According to **[RFC 9110](https://httpwg.org/specs/rfc9110.html#field.content-length)**, Content-Length represents the length of the message body as a **decimal, non-negative integer** (measured in octets).  

Why is this necessary?  
- It allows recipients to determine when a message is complete and track its progress.  

One important detail from **[RFC 9112](https://httpwg.org/specs/rfc9112.html#body.content-length)** states that:  
- If a message **does not** have a Transfer-Encoding header, then Content-Length defines its length.  
- A sender **MUST NOT** include a Content-Length header in a message that also contains a Transfer-Encoding header.


#### **Transfer-Encoding**  
As specified in **[RFC 9112](https://httpwg.org/specs/rfc9112.html#field.transfer-encoding)**, the Transfer-Encoding header defines how the message body is encoded for transfer. The most common value is **chunked** encoding.

**How does chunked encoding work?**  
- The body is sent in a series of chunks.  
- Each chunk begins with its **size in hexadecimal**, followed by the actual data.  
- A chunk of size 0 signals the end of the message.  

This encoding enables:
- **Progressive processing** (recipients can start handling data before receiving the full message).  
- **Dynamic content transmission** (useful when the final content size is unknown).  
- **Persistent connections** (keep-alive), improving HTTP efficiency.  

  
## Handling Transfer-Encoding and Content-Length in HTTP

Even though the specification states that a sender **MUST NOT** include a Content-Length header in a message that also contains a Transfer-Encoding header, this is not always enforced in practice. This issue was resolved in HTTP/2, where Transfer-Encoding is no longer used.

### What happens if both headers are present in a request?

Part **[6.3](https://httpwg.org/specs/rfc9112.html#message.body.length)** of RFC 9112 discusses a potential attack scenario and provides guidance on handling such cases. Specifically, point 3 states:

> **If a message is received with both a Transfer-Encoding and a Content-Length header field, the Transfer-Encoding overrides the Content-Length. Such a message might indicate an attempt to perform request smuggling (Section 11.2) or response splitting (Section 11.1) and ought to be handled as an error. An intermediary that chooses to forward the message MUST first remove the received Content-Length field and process the Transfer-Encoding (as described below) prior to forwarding the message downstream.**

Thus, the specification mandates that servers must be aware of both headers. If both are present, the Content-Length header should be removed to prevent misinterpretation.

### Why is this a security concern?

When the HTTP specification was initially created in 1997, infrastructures were simpler, and most websites were handled by a single server. However, modern web architectures rely on multiple components such as proxies, load balancers, and security gateways. Some of these components do not fully support Transfer-Encoding, or they may interpret conflicting headers differently.

This inconsistency in how different servers and intermediaries handle these headers creates an opportunity for **HTTP request smuggling attacks**. In such attacks, an adversary manipulates headers to cause a server to interpret requests differently from downstream components, potentially leading to security breaches such as cache poisoning, unauthorized request execution, or bypassing authentication mechanisms.

 

## 3. Identification
At this stage, what do we know? For this vulnerability to occur, multiple servers must be in a request chain, and they must handle headers inconsistently.

The first step is to enumerate proxy servers using tools like traceroute or nslookup. Additionally, the presence of headers like Via or X-Forwarded-For may indicate a multi-hop setup involving proxies or load balancers.

Using Burp Suite, the HTTP Request Smuggler extension can automatically send crafted requests to detect vulnerabilities. But what happens under the hood?

### **Basic Attack Techniques**
Before we proceed, let's go over the three primary types of HTTP request smuggling attacks:

 - **CL.TE** (Content-Length vs. Transfer-Encoding mismatch)
The frontend server processes Content-Length (CL), while the backend server processes Transfer-Encoding (TE).
 - **TE.CL** (Transfer-Encoding vs. Content-Length mismatch)
The reverse of the above: the frontend processes Transfer-Encoding, while the backend relies on Content-Length.
 - **TE.TE** (Two conflicting Transfer-Encoding headers)
Different servers in the chain handle the multiple Transfer-Encoding headers inconsistently.


There are some prerequisites for identifying these vulnerabilities:
 - 1. The attack surface applies only to HTTP/1.1.
 - 2. In Burp Suite, navigate to Repeater, switch the HTTP version to 1.1, and verify whether the request is supported.
 - 3. Enable the option to display non-printable characters.
 
`\r\n` (Carriage Return + Line Feed) at the end of each line. HTTP follows legacy Telnet/MIME standards, where \r\n marks the start of a new line.
An additional \r\n after the headers marks the transition between headers and the request body.


### **Understanding Chunked Encoding**
Before testing, it's important to understand the chunked transfer encoding format. According to the HTTP specification, a chunked body is structured as follows:

```bash
chunked-body   = *chunk
                 last-chunk
                 trailer-section
                 CRLF

chunk          = chunk-size CRLF
                 chunk-data CRLF
```

The chunk size is a hexadecimal value specifying the length of the chunk data.
The trailer section may contain additional headers, but for this test, we will ignore them.
A correctly formatted chunked body looks like this:

```bash
3\r\n
abc\r\n
0\r\n
\r\n
```

Now, let’s proceed with testing.

Identifying the Vulnerability
To test for request smuggling, send the following request:

```bash
Content-Length: 6
Transfer-Encoding: chunked
\r\n
3\r\n
abc\r\n
X\r\n
\r\n
```

Here’s what's happening:

The body has 13 bytes, but the Content-Length: 6 header tells the server to expect only 6 bytes (abc\r\n).
The presence of X after abc may reveal an inconsistency in how the frontend and backend interpret the request.
Possible Responses
Immediate rejection:

The frontend correctly recognizes Transfer-Encoding: chunked and interprets X as an invalid chunk size (not hexadecimal).
This suggests the server correctly processes TE and is likely not vulnerable.
Correct response (no smuggling detected):

This means the frontend and backend both rely on Content-Length, making the application resistant to request smuggling.
Timeout:

The backend ignores Content-Length, interprets 3 abc as a chunk, but fails on X, as it's not a valid chunk size.
The backend waits indefinitely for a valid chunk size, which never arrives.
This indicates a TE.CL vulnerability.
Confirming TE.TE vs. TE.CL
To determine whether the application is vulnerable to TE.TE or TE.CL, send a second request:

```bash
Content-Length: 6
Transfer-Encoding: chunked
\r\n
0\r\n
\r\n
X
```

Possible Responses
Correct response:

This suggests either TE.TE or CL.CL handling.
CL.TE behavior:

The frontend forwards the entire body, but the backend leaves X in the buffer.
If a follow-up request is sent, it may cause an invalid method error like XPOST, showing that X was treated as the beginning of the next request.
Timeout:

The frontend ignores Content-Length, meaning X is never sent.
The backend expects a total body length of 6 but never receives it.
This confirms a TE.CL vulnerability.

HTTP Request Smuggler identification
Detecting TE.TE


## 5. Exploiting diffrent types of HTTP Request Smuggling
CL.TE exploitation
TE.CL explotation
TE.TE explotation
CL.0

## 6. Conclusion
- Summary of key points.  
- The evolving nature of HTTP and new security challenges.
- How to defend  
- Encouraging proactive security testing and mitigation efforts.  
