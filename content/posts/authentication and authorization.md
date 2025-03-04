---
title: "Web Authentication and Authorization Mechanisms"
description: "This article explores web authentication and authorization mechanisms, covering methods like HTTP Basic, Session-Based, Token-Based (JWT, OAuth, OpenID), OTP, and WebAuthn. It details session management, secure cookie attributes, and key attack vectors such as session hijacking, CSRF, XSS, token leakage, and phishing, with mitigation strategies. Best practices include HTTPS enforcement, MFA, session expiration, token revocation, and least privilege access, making it a valuable resource for developers and security professionals."
showTableOfContents: true
tags:
  - Authentication
  - Authorization
  - Cookies
  - oAuth
  - JWT
  - OpenID
type: post
draft: true
date: 2025-02-23
---

## 1. Introduction
- Importance of authentication and authorization in web security
- Overview of modern authentication mechanisms
- Attackers' motivation and common security pitfalls

## 2. Authentication vs Authorization

Authentication and Authorization are two fundamental concepts in security that address different aspects of user access control.

### Authentication: Who Are You?

Authentication is the process of verifying a user's identity. It's essentially the system asking, "Who are you?" When we authenticate, we provide data that allows the system to confirm our identity with certainty. This could involve:

- Entering a username and password
- Using biometric data (fingerprint, facial recognition)
- Providing a security token

### Authorization: What Can You Do?

Once authenticated, authorization comes into play. It determines what actions an authenticated user is permitted to perform within the system. When we try to perform actions like uploading a photo or accessing a specific path, the system asks, "I know who you are, but are you allowed to do this?"

### Key Differences
To summarize the distinction:
- **Authentication** - verifying user identity
- **Authorization** - granting or restricting access


## 3. Types of Authentication

So as we all use webistes and creating accounts, we know that basic type of authentication is providing username and password. Bu underneath of this are many techinques working in the background non visible for the user let's shortly get to know all of them

### 3.1 HTTP Basic Authentication

The simplest one, present firsty in 1992 and is present is http specification since 1996. It's not require login pages, cookies or tokens. All the data are transfered inside header Authorization:

```bash
Authorization: Basic <encoded64(username:password)>
```

Basically server when see the header with word Basic decode value and compare with with database or other provider.

Not very secure, in usage for internal or legacy systems not recommended on production.

### 3.2 HTTP Digest Authentication

In contrast to the Basic method it use hashing instead of reversiable encoding, so it's another step into more security. 
Let's assume client try to get the authenticated needed request:

```bash
GET /admin HTTP/1.1
Host: example.com
```

The server respond with 401 status code and WWW-Authentication header. 

```bash
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Digest realm="example.com", qop="auth", nonce="dcd98b7102dd2f0e8b11d0f600bfb0c093", opaque="5ccc069c403ebaf9f0171e9517f40e41"
```

The elements for header:
- word Digest
- realm, string that tell the client what resource he authenticate for
- qop, auth for authentication only and auth-int adds layer and integrity protection qop is used, the client includes a client nonce (cnonce) and a nonce count (nc) in the response, which adds randomness and prevents replay attacks
- nonce, random value generated for single authentication
- opaque, another security feature when present the client should send it back when authenticate


```bash
Authorization: Digest username="user", realm="example.com", nonce="dcd98b7102dd2f0e8b11d0f600bfb0c093", uri="/admin", response="6629fae49393a05397450978507c4ef1", qop=auth, nc=00000001, cnonce="0a4f113b"
```

Most important in the client response is value of response where is composed as:
- HA1=MD5("user:example.com:password")
- HA2=MD5("GET:/admin")
- response=MD5(HA1:nonce:nc:cnonce:qop:HA2)

This type is more secure than basic type, but still not very common. Can be useful for some specific use cases.

### 3.3 Session-Based Authentication

There are many security attributes and mechanism for session-based authentication, but the basic flow work as below:
1. User sends credentials to the server
2. Server verifies credentials against owned data
3. Server creates new session and save it
4. Server sends back session ID to the client
5. Client saves session ID for further usage
6. Client request for proctected resources
7. Server compare sessions ID with those that it stores
8. Server authorize access to the client


Server uses `Set-Cookie` header to set session ID for user. Then user each time accessing protected resources will send `Cookie` header with session ID inside.

This is the basic of session-based authentication, many security mechanism sits above of this scheme. The most common are: CSRF protection, cookie flags, session rotation policy, which will be considere in next chapter.

Widely used especially with server-side frameworks, especially for it's simplicity and flexibility.

### 3.4 Token-Based Authentication

The session-based type makes server store active session IDs, making it difficult for growing and dynamic server infrastructure that grows horizontaly.

For token-based authentication the flow is very similar but value is stored not as a id of session but as a token which is signed with cryptographic keys.
Tokens are store on the local storage of web browser. The flow looks like that:

1. User sends credentials to the server
2. Server verifies credentials against owned data
3. Server creates new token and sign it
4. Server sends token to the client
5. Client saves token on local storage
6. Client request for proctected resources
7. Server decrypt token and check token
8. Server authorize access to the client

Common for security APIs in distributed systems. Very popular in modern web and mobile apps, SSO systems and IoT.

### 3.5 One Time Passwords (OTP)

The risk of brute force attack and stealing of password, was one of a reason to create second factor for authentication.
Beside something you know - your credentials, second factor tell the server what we have email or phone.
The basic flow looks like:

1. Client login with credentials
2. Server authenticates user and create 6-digit code.
3. OTP code is sent to the mail or mobile phone
4. Client enter code on the page
5. Server verifies code
6. Server authorize access

Used widely as a second factor and aditional layer of security.

### 3.6 OAuth 2.0

Very common when using third-party integrations and API access.

### 3.7 OpenID Connect (OIDC)

In production a layer on top of oAuth 2.0.

### 3.8 WebAuthn (FIDO2)

It's growing in production as a phishing resistant method.

### 3.9 Magic Links

Not very standard for normal use cases. Sometimes useful.

## 4. Session Management
- How Sessions Work
- Server-side vs Client-side Sessions

## 5. Cookies: Security Attributes & Best Practices
- HttpOnly, Secure, SameSite
- Domain & Path
- Max-Age & Expires

## 6. Authentication & Session Attacks
### 6.1 Session Hijacking
### 6.2 Session Fixation
### 6.3 CSRF (Cross-Site Request Forgery)
### 6.4 XSS (Cross-Site Scripting) & Token Theft
### 6.5 Token Leakage & Replay Attacks
### 6.6 Credential Stuffing
### 6.7 Brute Force Attacks
### 6.8 Man-in-the-Middle (MitM) Attacks
### 6.9 Phishing Attacks
### 6.10 OAuth Token Abuse
### 6.11 Session Prediction
### 6.12 API Key Leakage

## 7. Best Practices for Secure Authentication & Authorization
- HTTPS everywhere
- Strong password policies + MFA
- Proper session expiration & token revocation
- Secure cookie attributes
- Monitoring and logging
- Least privilege access control
- Regular security audits and penetration testing

## 8. Implementation Examples
- JWT implementation
- OAuth2 flow
- WebAuthn integration

## 9. Comparison of Authentication Mechanisms
- Table comparing different methods (advantages, disadvantages, use cases)

## 11. Regulatory Compliance
- GDPR considerations
- PSD2 and Strong Customer Authentication (SCA)

## 12. Authentication in Microservices Architecture
- Challenges in distributed environments
- Service-to-service authentication
- API gateways and authentication

## 13. Security Testing for Authentication Systems
- Penetration testing methodologies
- Automated security scanning
- Common vulnerabilities to test for

## 14. Real-world Use Cases
- Google's authentication ecosystem
- Facebook's authentication and authorization
- Banking sector authentication practices

## 15. Future Trends in Web Authentication
- Passwordless authentication
- Blockchain-based identity management
- Advancements in WebAuthn and Passkeys

## 16. Conclusion
- Summary of key authentication mechanisms
- Importance of proper implementation
- Balancing security and user experience