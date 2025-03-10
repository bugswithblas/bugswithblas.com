---
title: "Session and Token based web authentication"
description: "This article explores web authentication and authorization mechanisms, covering methods like HTTP Basic, Session-Based, Token-Based (JWT, OAuth, OpenID), OTP, and WebAuthn. It details session management, secure cookie attributes, and key attack vectors such as session hijacking, CSRF, XSS, token leakage, and phishing, with mitigation strategies. Best practices include HTTPS enforcement, MFA, session expiration, token revocation, and least privilege access, making it a valuable resource for developers and security professionals."
showTableOfContents: true
tags:
  - Authentication
  - Authorization
  - Cookies
  - oAuth
  - JWT
  - Tokens
  - OpenID
type: post
draft: true
date: 2025-02-23
---

## 1. Introduction

In the digital age, where web applications handle sensitive data, robust authentication mechanisms are crucial. 
Session and token-based authentication protocols form the backbone of user identity management, yet they remain vulnerable to exploitation. 
As penetration testers and security practitioners, understanding these models is key to safeguarding systems. 
Both approaches have unique vulnerabilities making them prime targets for attackers. 
This article will explore the intricacies of these authentication methods, common vulnerabilities, and strategies for hardening web applications against evolving threats.

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


## 3. Introduction of mechanisms

### 3.1 Session-Based Authentication

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
This is the basic of session-based authentication, many security mechanism sits above of this scheme. 
The most common are: CSRF protection, cookie flags, session rotation policy, which will be considere in next chapter.


### 3.2 Token-Based Authentication

For token-based authentication the flow is very similar but value is stored not as a id of session but as a token.
The flow looks like that:

1. User sends credentials to the server
2. Server verifies credentials against owned data
3. Server creates new token and sign it
4. Server sends token to the client
5. Client saves token on local storage
6. Client request for proctected resources
7. Server decrypt token and check token
8. Server authorize access to the client

### 3.3 JWT tokens


### 3.4 OpenID Connect (OIDC)

To understand OpenID firtly we must know what oAuth 2.0 is.
It's a method that allow share data to third party app without need to send password.
It allow for example a app which allow to upload photo get access to my photos inside Google Photos giving this app permission to the access.
Under the hood this third party app get access token.

Let's assume we have user Mike who have Google Photos account with photos from holiday and want to create album from those photos on web app GreatAlbums,
which allow him not to send photos but used those already present on Google Photos.

In this example:
Mike is `Resource Owner` with access to Google Photos. GreatAlbums app is a `Client`.
`Authorization Server` is a special oAuth server on Google side which ask for access Mike and sends `access token` to client.
`Resource Server` is a server that host user photos on Google Photos account.

The flow work like that:
1. Client redirect user to authorization server and ask for permission to the specified resources
2. User need to approve access, if you do not have active session it asks for credentials
3. Authorization server issue authorization code
4. Client sends authorization code for resource owner with client id and client secret
5. Authorization server verify client id, client secret and authorization code
6. Authorization server sends access token to the client
7. Client can access resources with access token

What about OpenID? It's just a use case of oAuth but the resources that are shared for this third party app are user info allowing to user existing account like Google
to access this third party website. The flow are very similar as with oAuth but with few diffrences:

1. While client redirect user it adds `scope=openid`
2. Authorization server send `ID Token` with access token

ID tokens are required to be in format of JWT

## 4. Session Management Vulnerabilities Deep Dive
Attack Vectors:
- Session Hijacking via XSS
  Exploiting reflected/stored XSS to steal session cookies (demonstrate DOM-based token leakage)111

- Session Fixation
  Techniques to force session IDs (e.g., URL rewriting, hidden form fields) with PoC using modified Set-Cookie headers313

- Client-Side Session Poisoning
  Manipulating session storage through prototype pollution or insecure deserialization112


## 5. JWT Implementation Pitfalls
 - Algorithm Confusion Attacks
  alg:none bypass and RSA/HMAC confusion (demo with PyJWT library)715

- JWK/JKU Header Injection
  Forging tokens using external key servers (practical example with modified jku header)714

- Kid Parameter Manipulation
  Path traversal via kid header to load arbitrary keys715

## 6. OAuth/OpenID Connect Attack Surface

- Redirect URI Manipulation
  Chaining open redirects to steal auth codes (step-by-step flow hijacking)810

- Dynamic Client Registration Abuse
  SSRF via malicious logo_uri or jwks_uri parameters10

- Scope Parameter Pollution
  Privilege escalation through modified scope values8

Real-World Case Study:
2024 Microsoft Consent Phishing Campaign leveraging malicious OAuth apps9

## 7. Token Storage & Transmission Risks

- LocalStorage XSS Exfiltration
  Stealing persistent tokens via DOM-based vulnerabilities6

- Service Worker Hijacking
  Intercepting token refresh mechanisms6

- Compromised Refresh Tokens
  Chaining token replay with revoked access tokens4

## 8. Cryptographic Implementation Flaws
- Weak HMAC secrets (brute-force with hashcat)
- RSA key reuse across environments
- ECB mode encryption pattern analysis14

## 9. Penetration Testing Methodology

### 9.1 Cookie Attributes Analysis
HttpOnly, Secure, and SameSite testing

### 9.2 OAuth Flow Validation
State parameter checks & PKCE verification

### 9.3 Token Entropy Testing
Statistical analysis of JTI values

### 9.4 Token Lifetime Analysis
Expiration/refresh timing attacks56


6. Defense Tactics for Developers
Hardening Strategies:

- Session Binding Techniques:
  IP fingerprinting
  User-Agent validation
  Session migration prevention

JWT Security Checklist:

```text
1. Enforce `alg` whitelist
2. Validate `aud` and `iss` claims
3. Implement strict jku validation
4. Use asymmetric signatures by default
```
