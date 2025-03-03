---
title: "Understanding Request Smuggling"
description: "HTTP request smuggling is a powerful attack that exploits inconsistencies in how front-end and back-end servers parse HTTP requests. This article provides a deep dive into the fundamentals, covering key HTTP headers like Content-Length and Transfer-Encoding, and explaining how parsing discrepancies create security risks. We explore how modern web architectures contribute to these vulnerabilities and demonstrate both manual and automated."
showTableOfContents: true
tags:
  - "Request Smuggling"
  - HTTP
type: post
draft: false
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
 - 3. Enable the option to display non-printable characters. You'll see `\r\n` (Carriage Return + Line Feed) at the end of each line. HTTP follows legacy Telnet/MIME standards, where \r\n marks the start of a new line.
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

Where first chunk of size 3 sends value `abc` and next chunk with size 0 and CRLF at next line tells server that it's last chunk.

Now, let’s proceed with testing.

**Identifying the Vulnerability**

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

The body has 13 bytes, but the Content-Length: 6 header tells the server to expect only 6 bytes.
The presence of X after abc may reveal an inconsistency in how the frontend and backend interpret the request.

**Possible Responses**

- 1. Immediate rejection: The frontend correctly recognizes Transfer-Encoding: chunked and interprets X as an invalid chunk size (not hexadecimal).This suggests the server correctly processes TE. But we cannot say how backend is reacting. In this situation is either TE.CL or TE.TE

- 2. Correct response: This means the frontend and backend both rely on Content-Length, making the application resistant to request smuggling.

 - 3. Timeout: The backend ignores Content-Length, interprets abc as a chunk, but fails on X, as it's not a valid chunk size. The backend waits indefinitely for a valid chunk size, which never arrives. This indicates a TE.CL vulnerability.



**Confirming TE.TE vs. TE.CL**

To determine whether the application is vulnerable to TE.TE or TE.CL, send a second request:

```bash
Content-Length: 6
Transfer-Encoding: chunked
\r\n
0\r\n
\r\n
X
```

**Possible Responses**

 - 1. Correct response: This suggests either TE.TE or CL.CL handling.

 - 2. CL.TE behavior: The frontend forwards the entire body, but the backend leaves X in the buffer. If a follow-up request is sent, it may cause an invalid method error like XPOST, showing that X was treated as the beginning of the next request.

 - 3. Timeout: The frontend ignores Content-Length, meaning X is never sent. The backend expects a total body length of 6 but never receives it. This confirms a TE.CL vulnerability.


**Automatic Identification**

I showed you how to manually test applications with those two requests. But it's done by the Burp Suite extension **[HTTP Request Smuggler](https://portswigger.net/bappstore/aaaa60ef945341e8a450217a54a11646)**. To run the extension, right-click on the request and Extensions -> HTTP Request Smuggler -> Smuggle Probe. In the target tab, I got the message: 

> "Possible HTTP Request Smuggling: CL.TE multiCase (delayed response)."

And in one of the requests below, it's a request similar to those shown above:


```bash
Content-Length: 13
tRANSFER-ENCODING: chunked
\r\n
3\r\n
x=y\r\n
1\r\n
Z\r\n
Q\r\n
\r\n
```

The response from this request is a timeout. Why is it suggesting it's CL.TE? Similar to the previous case, the frontend completely ignores TE, but the backend does not. As it gets Q as a chunk size, it waits for the proper value and responds with a timeout.

```bash
Content-Length: 14
tRANSFER-ENCODING: chunked
\r\n
3\r\n
x=y\r\n
0\r\n
\r\n
X
```

This time, automatic detection for TE.CL occurs. A very similar approach is used. The frontend ignores X as it uses TE and detects the last chunk. The backend, however, waits for the 14th byte based on the CL value but never receives it, resulting in a timeout.

**Detecting TE.TE**

As we showed above, TE.TE can be successfully detected with the first two requests. This situation happens when both the backend and frontend use TE, and we can obfuscate headers to force one of the servers not to process it. As explained on PortSwigger Academy:

"Each of these techniques involves a subtle departure from the HTTP specification. Real-world code that implements a protocol specification rarely adheres to it with absolute precision, and it is common for different implementations to tolerate different variations from the specification."

So it's up to the attacker to find subtle differences that allow a header to be ignored. As you can see in the example above from the extension, it uses an obfuscation technique by changing all the characters to uppercase except the first letter: tRANSFER-ENCODING: chunked. The extension tries to perform multiple identifications at once, so it combines multiple techniques into a single request.

There are many techniques to obfuscate the TE header. Here are a couple of them:

```bash
Transfer-Encoding: xchunked

Transfer-Encoding: x

Transfer-Encoding:[tab]chunked

[space]Transfer-Encoding: chunked

X: X[\n]Transfer-Encoding: chunked

Transfer-Encoding
: chunked

Transfer-encoding: identity

Transfer-encoding: cow

tRANSFER-ENCODING: chunked
```


## 4. HTTP/2 Request Smuggling

HTTP/2, while maintaining the same methods, status codes, and headers as its predecessor, fundamentally changes how data is formatted and transmitted. Unlike the text-based HTTP/1, HTTP/2 is a binary protocol, sending data as a collection of 0s and 1s. In the context of request smuggling, it's crucial to note that HTTP/2 doesn't use Transfer-Encoding or Content-Length headers in the same way. Instead, each frame specifies its length in bytes at the beginning, as defined in the HTTP/2  **[frame format](https://datatracker.ietf.org/doc/html/rfc7540#section-4.1)**



### Http2 downgrading

The vulnerability of HTTP/2 to request smuggling primarily stems from the process of HTTP/2 downgrading. Many organizations implement this downgrading for backward compatibility with legacy backend systems that haven't yet transitioned to HTTP/2. This flexibility allows administrators to maintain access to older systems while using modern frontend solutions.

However, this backward compatibility introduces security risks. When downgrading occurs, the backend loses access to the HTTP/2 frame length value, potentially allowing the injection of HTTP/1 headers.



### H2.CL and H2.TE

In HTTP/2, requests use pseudo-headers instead of the traditional request line. For example, the path is represented by the :path pseudo-header, and the Host header is replaced by :authority. Here's an example of how an attacker might attempt to smuggle a request:


```bash
:method POST
:path /test
:authority example.com
content-type application/x-www-form-urlencoded
transfer-encoding chunked

0

GET /admin HTTP/1.1
Host: example.com
Foo: Injected
```

When downgraded to HTTP/1.1, this request might look like:


```bash
POST /test HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: example.com
Foo: Injected
```

In this scenario, the backend might interpret the body as a zero-length chunk, treating the rest as the beginning of a new request. This could potentially allow access to restricted paths like /admin.

Similarly, an attacker could attempt to inject a Content-Length header:

```bash
:method POST
:path /
:authority example.com
content-type application/x-www-form-urlencoded
content-length 0

GET /admin HTTP/1.1
Host: example.com
```

When downgraded, this becomes:


```bash
POST / HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 0

GET /admin HTTP/1.1
Host: example.com
```

Here, the body of the request might be ignored due to the Content-Length being set to zero, potentially allowing the smuggled request to be processed.

There are many of possible injections points you can try, there is a great article about it by James Kettle: **[HTTP/2: The Sequel is Always Worse](https://portswigger.net/research/http2)**. In this article i want to just show basic mechanism.

## Conclusion

We’ve established a solid foundation for understanding HTTP request smuggling. We’ve examined essential HTTP headers, analyzed how discrepancies between front-end and back-end processing can introduce vulnerabilities, and explored both manual and automated methods for detecting these issues. A key aspect of this is understanding the role of Content-Length and Transfer-Encoding in request handling.

Understanding HTTP/2 request smuggling requires knowledge of how HTTP/2 differs from HTTP/1, particularly in terms of data formatting and transmission. The vulnerability primarily arises from the downgrading process implemented for backward compatibility. By manipulating headers and taking advantage of how different servers interpret requests, attackers can potentially smuggle requests even in HTTP/2 environments.
