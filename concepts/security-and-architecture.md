---
title: Security & Architecture
description: Understanding PulseBeam Security and Architecture
tableOfContents: { minHeadingLevel: 1, maxHeadingLevel: 4 }
---

PulseBeam provides a real-time communication platform, leveraging WebRTC's robust encryption with additional layers of protection to keep your real-time communication safe. We have a shared responsibility model and work with you to secure your projects and data. Below is an outline of the security principles, architecture, features, and practices that we provide.

This overview is for developers using PulseBeam. You will need a basic understanding of security and architecture. You can find a checklist summarizing and reiterating recommendations at the end.

Terms we will use:

* YOU/YOUR — Refers to the intended reader, developers using PulseBeam CPaaS. You log in to the console and get keys to write web applications. You use `@pulsebeam/server` and `@pulsebeam/peer` SDKs to create applications.
* APPLICATION/PROJECT - the app you are creating with PulseBeam.
* PEER/CUSTOMER/USER/END-USER - e.g. Alice and Bob the intended audience for your application
* WE/US/PULSEBEAM - PulseBeam, us, the CPaaS provider
* SECRETS/PRIVATE KEYS - the private key in the asymmetric key set provided by PulseBeam to you

# Architecture Overview

Here is an example architecture using PulseBeam. In this example, you have two customers, Alice and Bob, accessing your web application to create a real time peer to peer connection with PulseBeam. We will use this to explore PulseBeam's architecture and security properties.

![Sample Peer to peer architecture sequence diagram](../../../../assets/concepts.security.infra.svg)

## Core Components

PulseBeam architecture consists of several key components:

* **Your Projects**: Your applications that use PulseBeam. For example, your website which Alice and Bob visit.
* **Signaling Servers**: PulseBeam servers facilitating connection establishment. For Alice and Bob's connection.
* **STUN/TURN Servers**: PulseBeam network traversal and relay services. For Alice and Bob's alternate path
* **Authentication Services**: Token-based access control system. Issued by you, for Alice and Bob to use PulseBeam. 
* **WebRTC Communication**: Peer-to-peer encrypted communication channels. Real-time data transmission for Alice and Bob to communicate.

## Typical Security Flow

Explore our documentation for code samples. See [Quick Start](/docs/getting-started/quick-start/) for a sample project using `@pulsebeam/server` and `@pulsebeam/peer` SDKs. See Reference for [peer](/docs/reference/peer-js/) and [server](/docs/reference/server-js/) SDK documentation and more details.

### Initial Authentication

1. Customers access your application, typically this will be a web app.
1. Note: Web apps must use HTTPS
1. Customers hit your server/serverless endpoint hosting tokens
1. Your endpoint uses PulseBeam Credentials with our `@pulsebeam/server` SDK to generate tokens for your customers
1. Encrypt all traffic on your endpoint (e.g. HTTPS) so tokens are safe in transit.
1. We recommend you authenticate customers before issuing them tokens. You may want to authorize them as well.
1. Note: Tokens are intended to be transient, directly returned to the client.

### User Session Establishment

1. Customers use the tokens you gave them to access PulseBeam services. Typically, using `@pulsebeam/peer` SDK
1. You must use your private key, to generate valid PulseBeam tokens.
1. PulseBeam authorizes requests based on the security policy you set on the token. 
1. PulseBeam services establish WebRTC's connections

### Secure Communication

1. PulseBeam Signaling services operates over HTTPS and establish WebRTC connections.
1.  WebRTC enforces encryption of all communication (e.g. media and data streams) over every connection
1. When direct peer to peer fails due to customer network restrictions (e.g. firewall), PulseBeam TURN servers relay encrypted traffic without ability to decode customer data.

# Security Features and Controls

To improve your security profile, you should restrict permissions to the minimum permissibility required by your project. PulseBeam provides:

* Token-based authentication system
* Security policies on tokens
* TTL (time-limited access) on tokens
* Project-level access controls and keys

### Customer Tokens, TTL, Policies

* Our platform supports token-based authentication for your users to access PulseBeam resources.
* You control and generate tokens. 
* You create private keys on your project. Private keys to allow you to generate tokens for your customers.
* You define the security policy on the token. We enforce policies that you set on the token.
* Security policies restrict which peer(s) the peer you minted the token for can connect to. 
* We recommend more restrictive for higher security. To reduce potential impact of leaked tokens and prevent unintended usage.
* You provide tokens to users, we recommend you authenticate users before providing them with tokens, to prevent spoofing and impersonation. See more in the [Authentication](#peer-authentication-identity-and-authorization) section.
* You define a TTL on the token. We recommend shorter TTLS for higher security. As it reduces potential impact of leaked tokens
* You should manage token lifecycle in your client. By monitoring token session expiration time and refreshing the token before it expires.
* You can immediately invalidate all tokens minted by your key by deleting the key from your PulseBeam project. PulseBeam does not currently support individual token revocation.

### Project Access Control

* We recommend a 1:1 relationship between people and PulseBeam accounts.
* A PulseBeam account can have many PulseBeam projects.
* PulseBeam Projects can have many keys.
* Many PulseBeam accounts can share ownership of one PulseBeam Project.

Here is a sample of how you could manage a production application with project access controls.

![Sample PulseBeam Account and Project Access Control Management - Account 1 has two projects. All other accounts have one. Both projects have multiple keys. For example prod would have Developer 1's key and a Server key
](../../../../assets/concepts.security.accounts.svg)

## PulseBeam Project Keys 

* Your PulseBeam keys are asymmetric.
* Use your private key to generate tokens for your customers. Only you have a copy of your private key.
* Be sure to protect your keys. Especially your private key!
* When you create a key set, we store a copy of your public key.
* We use your public key to verify that tokens we receive were created by your private key.
* Note: You can use your public key to identify key set(s) on your PulseBeam projects when rotating keys.
* We recommend rotating your keys regularly. 

# Peer Authentication, Identity, and Authorization

You should know the identity of whomever you issue tokens to for many reasons:
* Allows you to secure your system: 
    * Control usage. Usage on your PulseBeam account can create bills
    * Detect and prevent bad actors
    * Ban and remove bad actors who appear
    * Allow peers to connect to other peers based on your desired application logic. You likely want to be able to verify the identity of their peers. E.g. if Alice wants to talk to Bob, making sure Bob is Bob, and you gave Bob's token to Bob is something you should ensure. And not an imposter of Bob!

Identity providers are one way to identify your users:
* OIDC Web-based identity providers (IdP) E.g.:
    * Google
    * Facebook
    * Github
* If you use an IdP, your authentication security will depend on:
    * users tie their authentication on other services which you may consider a "trusted" service.
    * Note that in this case the level of "trust" that an Identity Provider possesses is largely tied to the platform user base and reputation. 
    * Who your customer is
    * What your intended usage pattern your application logic.
* Other systems may be any manner of other things, one of which is username and password.

PulseBeam uses the token you issue to your customer to determine their identity and their permissions.

# Encryption and Data Protection

Encryption protects from sniffing and tampering, and is vital to security.

* WebRTC enforces encryption over the connection
* PulseBeam enforces encryption for traffic through our services

We recommend encrypting communications between your services and customers. This greatly improves your security profile.

## WebRTC Media/Data Encryption

Encryption on all communication is a mandatory in WebRTC:

* SRTP (Secure Real-time Transport Protocol) is enforced for media
* DTLS (Datagram Transport Layer Security) is enforced for data

These are both well known, studied, and standardized protocols.

End-to-end encryption is done through DTLS-SRTP key exchange, where keys are negotiated directly between the peers on each session.

Encryption with regular key rotation (automatic negotiation on session) and modern ciphers renders it effectively impossible for an eavesdropper to see the contents of communication.

Only parties with access to the secret encryption key can decode the communication streams.

A TURN server's purpose is simply the relay of WebRTC data between parties in a call as a fallback mechanism when peer to peer connections fail. TURN will only parse the UDP layer of a WebRTC packet for routing purposes. Servers will not access the application data layer in order to route packets. Since it is not a peer, we know that the TURN server does not (and cannot) touch the encryption (read the data).

DTLS-SRTP facilitates secure key exchanges, enabling detection of potential Man-in-the-Middle (MiTM) attacks.

Read more on DTLS and SRTP in [WebRTC for the Curious](https://webrtcforthecurious.com/docs/04-securing/) and [the standards](https://datatracker.ietf.org/doc/rfc8827/).

This is done through two more protocols that also pre-date WebRTC; DTLS (Datagram Transport Layer Security) and SRTP (Secure Real-Time Transport Protocol). The first protocol, DTLS, is simply TLS over UDP (TLS is the cryptographic protocol used to secure communication over HTTPS). The second protocol, SRTP, is used to ensure encryption of RTP (Real-time Transport Protocol) data packets.

First, WebRTC connects by doing a DTLS handshake over the connection established by ICE. Unlike HTTPS, WebRTC doesn't use a central authority for certificates. It simply asserts that the certificate exchanged via DTLS matches the fingerprint shared via signaling. This DTLS connection is then used for DataChannel messages.

Next, WebRTC uses the RTP protocol, secured using SRTP, for audio/video transmission. We initialize our SRTP session by extracting the keys from the negotiated DTLS session.

We will discuss why media and data transmission have their own protocols in a later chapter, but for now it is enough to know that they are handled separately.

Now we are done! We have successfully established bidirectional and secure communication. If you have a stable connection between your WebRTC agents.

## Signaling Security

* PulseBeam uses HTTPS to encrypt all signaling traffic
* Signaling uses Token validation for all resource access
* Tokens ensures only users whom you issued tokens to can participate in connections.
* This protects user data like SDPs

## TURN / STUN Security

You can conceptualize the TURN server as an intermediary router between the two peers. 

PulseBeam TURN servers cannot decode application-level data because encryption on all communication is a mandatory in WebRTC. Only the two peers participating in the call have ability to decrypt.

WebRTC connections over an intermediary TURN server do not compromise encryption. The server is unable to decode application-level data, it cannot understand or modify information that peers send to each other. Ensuring encryption and privacy remains intact during relay.

Our TURN servers by default only relay traffic as a backup for routing purposes when peer network infrastructure prevents a direct peer to peer connection.

# Shared Security Responsibility Model

Security is a shared responsibility. While PulseBeam provides a secure platform, you play a crucial role in safeguarding end-user data and experience.

![Shared Responsibility Model Overview Diagram](../../../../assets/concepts.security.shared.svg)

## PulseBeam Responsibilities

* Infrastructure Security: Ensuring servers (e.g., signaling servers, TURN servers, STUN servers) and storage systems are encrypted, secure, and reliable.
* Platform encryption. Encrypting traffic through our services as well as between your end users and end users.
* Platform Uptime and Reliability: Maintaining secure and available infrastructure for service meeting or exceeding SLAs.
* Token validation: Ensuring traffic comes from users with valid tokens.
* PulseBeam Account: Prevent unauthorized access on your PulseBeam projects. Enforce sharing and access control to your settings.
* Security Policy Enforcement: Ensure traffic matches allow policies set by you on tokens you minted.

## Your Responsibilities

* Customer End-User Authentication: Authenticating your customers and ensuring only authorized users can access your projects(s).
* Security policy configuration: Setting policies on tokens to be as restrictive as possible.
* Token Generation and Management: Generating tokens for your end-users securely. Transmitting tokens securely to your end-users.
* Data Encryption: Protect end-user data. Encrypt communication between your platform and end-users. Use secure clients, HTTPS web clients, and encrypt communication with your customers.
* Key Management: Rotating keys regularly and implementing secure secret storage.
* PulseBeam Account Access Management: Protect your account, only give access to your project(s) with people you trust. People you give access to can delete projects, delete keys, and create keys. Keys allow token generation.

## Shared Responsibilities

* PulseBeam Account: Safeguarding your accounts and data in our backend. You create accounts and projects. PulseBeam protects your account and data. Shares project access according to your allow list.
* Security Policy: You set policies on tokens PulseBeam enforces policies on traffic and usage. 
* Key Management: Protecting PulseBeam API keys and secrets. PulseBeam API Keys are asymmetric. We do not store your private keys. Private keys are only available on generation. PulseBeam does not store your private keys. You must store them securely. PulseBeam encrypts traffic including keys in transit.
* Security monitoring: Both you and PulseBeam monitor for security vulnerabilities.
* Incident response: Both you and PulseBeam cooperate to respond to security incidents.

# Security Checklist

By following these best practices, customers can protect the security of their applications and maintain end-user trust.

We encourage developers to adopt secure practices, especially for applications handling sensitive information. Best practices and recommendations include:

* <input type="checkbox" disabled /> Encrypting your traffic using secure encryption protocols for all client communications.
* <input type="checkbox" disabled /> Implement proper user authentication
* <input type="checkbox" disabled /> 1:1 relationship between people and PulseBeam accounts.
* <input type="checkbox" disabled /> Pre-registering users or requiring authentication before allowing participation in connections.
* <input type="checkbox" disabled /> Authenticate and authorize users before providing them with tokens.
* <input type="checkbox" disabled /> Implement short token TTL
* <input type="checkbox" disabled /> Secure token transmission
* <input type="checkbox" disabled /> Regular key rotation - 90 days may be a good timeline for you
* <input type="checkbox" disabled /> Strict policy configuration
* <input type="checkbox" disabled /> Communicating permission requests to users to prioritize privacy and user consent highlighting to the user what permission they are giving
* <input type="checkbox" disabled /> Follow secure coding practices
* <input type="checkbox" disabled /> Monitor for suspicious activities - Some examples of monitoring suspicious activity include: monitoring accounts, DAU, auditing account data, or usage in your own or PulseBeam metrics dashboard. Logs of peer's ID and groups and who they are connecting to. Ensuring logs are in line with your expected application logic.

# Conclusion

PulseBeam's security provides a robust foundation to secure real-time communications. PulseBeam leverages WebRTC's security features and supplements them with additional protections to deliver a secure, reliable communication platform. By following our shared responsibility model and implementing recommended security practices, you can ensure a high level of security for your applications and users.

# Learn more

[Contact us](/docs/community-and-support/discord/) for questions

Read more:

* [Google WebRTC Blog - Security Basics](https://web.dev/articles/webrtc-basics#security)
* [WebRTC's docs - Connection establishment, technical and detailed. 'How' behind the steps](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Connectivity)
* [WebRTC for the Curious - Security](https://webrtcforthecurious.com/docs/04-securing/)
* [Blog - Discussion on WebRTC Security](https://bloggeek.me/is-webrtc-safe/)
* [IETF - RFC 8827 - WebRTC Security Architecture](https://www.rfc-editor.org/rfc/rfc8827.html)
* [IETF - RFC 8827 - Security Considerations for WebRTC](https://datatracker.ietf.org/doc/rfc8827/)