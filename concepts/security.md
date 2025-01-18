---
title: Security
description: Understanding security in PulseBeam
---

# Security Overview for PulseBeam

PulseBeam provides a real-time communication platform, leveraging WebRTC's robust encryption with additional layers of protection to keep your real-time communication safe. We have a shared responsibility model and work together with you to secure your projects and data. Below is an outline of the security principles, architecture, features, and practices that we provide.

You will need a basic understanding of security. This is written for developers using PulseBeam. There is a checklist of recommendations at the end summarizing and reiterating recommendations.

Terms we will use:

* You/Your - Refers to developers using PulseBeam CPaaS, you login to console and get keys to write web applications. This documentation is written to be used by you, someone using @pulsebeam/server and @pulsebeam/peer SDKs to create applications.
* Application/Project the app you are creating with PulseBeam.
* Peer/Customer/User/End-User, e.g. Alice and Bob the indended audience for you application
* Us/PulseBeam - PulseBeam, us, the CPaaS provider

# 2. Security Architecture Overview

Here is an example architecture using PulseBeam. In this example, you have two customers, Alice and Bob, accessing your web application to create a real time peer to peer connection with PulseBeam. We will use this to explore PulseBeam's security properties.

![Sample Peer to peer architecture sequence diagram](../../../../assets/concepts.security.infra.svg)

## 2.1 Core Components

PulseBeam security architecture consists of several key components:

* Client Projects: Your applications that use PulseBeam. For example, your website which Alice and Bob visit.
* Signaling Servers: PulseBeam servers facilitating connection establishment. For Alice and Bob's connection.
* STUN/TURN Servers: PulseBeam network traversal and relay services. For Alice and Bob's alternate path
* Authentication Services: Token-based access control system. Issued by you, for Alice and Bob to use PulseBeam. 
* WebRTC Media Layer: Peer-to-peer encrypted communication channels. Real-time data transmission for Alice and Bob to communicate.

## 2.2 Typical Security Flow

### Initial Authentication

1. Customers access your application, typically this will be a web app.
1. Note web app is required to use HTTPS
1. Customers hit your server/serverless endpoint hosting tokens
1. Your endpoint uses PulseBeam Credentials with our @pulsebeam/server SDK to generate tokens for your customers
1. Encrypt all traffic on your endpoint (e.g. HTTPS) so tokens are safe in transit.
1. We recommend you authenticate Customers before issuing them tokens. You may want to authorize them as well.

### User Session Establishment

1. Customers use the tokens you gave them to access PulseBeam services. Typically, using @pulsebeam/peer SDK
1. PulseBeam checks the token ensuring it was created with your private key.
1. PulseBeam checks request and permissions. Request is authorized according to the security policy you set.
1. PulseBeam services establish WebRTC's connections

### Secure Communication

1. PulseBeam Signaling services operates over HTTPS and establish WebRTC connections.
1. All communication over WebRTC connections (media and data streams) are encrypted end-to-end
1. When direct peer to peer fails due to customer network restrictions (e.g. firewall), PulseBeam TURN servers relay encrypted traffic without ability to decode customer data.


# 3. Security Features and Controls

Restrict permissions to what is required by your application to improve your security profile. PulseBeam provides:
* Token-based authentication system
* Security policies on tokens
* TTL (time-limited access) on tokens
* Project-level access controls and keys

### 3.1 Customer Tokens, TTL, Policies

* Our platform supports token-based authentication for your users to access PulseBeam resources.
* You control and generate tokens. 
* You create private keys on your project. Private keys to allow you to generate tokens for your customers.
* You define the security policy on the token. We enforce policies that you set on the token.
* Security policies restrict which peer(s) the peer you minted the token for can connect to. 
* We recommend more restrictive for higher security. To reduce potential impact of leaked tokens and prevent unintended usage.
* You provide tokens to users, we recommend you authenticate users before providing them with tokens, to prevent spoofing and impersonation. Seem more in Authentication section.
* You define a TTL on the token. We recommend shorter TTLS for higher security. As it reduces potential impact of leaked tokens

### 3.2 Project Access Control

* We recommend a 1:1 relationship between people and PulseBeam accounts.
* A PulseBeam account can have many PulseBeam projects.
* PulseBeam Projects can have many keys.
* PulseBeam Projects can be shared with many PulseBeam accounts.

Here is a sample of how you could manage a production application with project access controls.

![Sample PulseBeam Account and Project Access Control Management - Account 1 has two projects. All other accounts have one. Both projects have multiple keys. For example prod would have Developer 1's key and a Server key
](../../../../assets/concepts.security.accounts.svg)

## 3.3 PulseBeam Project Keys 

* Your PulseBeam keys are asymmetric.
* Only you have your private key which is used to generate tokens for your customers. 
* Be sure to protect your keys. Especially your private key!
* We keep a copy of your public key and use it to verify that the token was created by you. As well as to identify the key set if you want to delete and rotate your keys.
* We recommend rotating your keys regularly. 

# 4. Peer Authentication, Identity, and Authorization

It is desirable for you to know the identity of whomever you issue tokens to. For many reasons:
* Allows you to secure your system: 
    * Control usage. Usage on your PulseBeam account can create bills
    * Detect and prevent bad actors
    * Ban and remove bad actors who appear
    * Allow peers to connect to other peers based on your desired application logic. You likely want to be able to verify the identity of their peers. E.g. if Alice wants to talk to Bob, making sure Bob is Bob, and you gave Bob's token to Bob is something you should ensure. And not an imposter of Bob!

This can be made possible through the use of identity providers or other systems
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

# 4. Encryption and Data Protection

Encryption protects from sniffing and tampering. Encryption is vital to security.

* WebRTC enforces encryption over the connection
* PulseBeam enforces encryption for traffic through our services

We recommend encrypting communications between your services and customers. This greatly improves your security profile. 

## 4.1 WebRTC Media/Data Encryption

Encryption on all communication is a mandatory in WebRTC:

* SRTP (Secure Real-time Transport Protocol) is enforced for media
* DTLS (Datagram Transport Layer Security) is enforced for data

These are both well known, studied, and standardized protocols.

End-to-end encryption is done through DTLS-SRTP key exchange, where keys are negotiated directly between the peers on each session.

Encryption with regular key rotation (automatic negotiation on session) and modern ciphers renders it effectively impossible for an eavesdropper to see the contents of communication.

Only parties with access to the secret encryption key can decode the communication streams.

A TURN server's purpose is simply the relay of WebRTC data between parties in a call as a fallback mechanism when peer to peer connections fail. TURN will only parse the UDP layer of a WebRTC packet for routing purposes. Servers will not access the application data layer in order to route packets. Since it is not a peer, we know that the TURN server does not (and cannot) touch the encryption (read the data).

DTLS-SRTP facilitates secure key exchanges, enabling detection of potential Man-in-the-Middle (MiTM) attacks.

Read more on DTLS and SRTP in WebRTC for the Curious and the standards.

## 4.2 Signaling Security

* All signaling traffic encrypted using HTTPS
* Token validation for all resource access
* Secure key exchange mechanisms

Signaling Layer Security

PulseBeam enforces encrypted signaling. 

The signaling layer facilitates communication setup but is not inherently secured by WebRTC. PulseBeam Signaling:

* We enforce encrypted signaling using HTTPS to secure signaling traffic.

These measures protect signaling data from interception and ensure only users whom you issued tokens can initiate or participate in sessions.

PulseBeam guarantees that signaling is encrypted preventing sniffing of customer data in transit, tampering of data providing both security.

## 4.3 TURN / STUN Security

Because keys are negotiated in an encrypted channel directly between the peers, the keys are only accessible to the peers. Therefore, PulseBeam TURN servers cannot decode application-level data

* Turn Server Security: Our TURN servers only relay traffic as a backup for routing purposes, are unable to decode application-level data, ensuring encryption remains intact during relay.

Resultantly, the protections put in place through encryption are therefore not compromised during WebRTC communication over TURN, and the server cannot understand or modify information that peers send to each other.

You can conceptualize the TURN server as an intermediary router between the two peers. Same principles of security apply, the traffic is encrypted.

# 6. Shared Security Responsibility Model

Security in real-time communication is a shared responsibility. While PulseBeam provides a secure platform, you play a crucial role in safeguarding end-user data.

* Customer Responsibilities:
    * End-User Authentication: Ensure only authorized users access your platform.
    * Encryption: Ensure you only use HTTPS, secure clients, and encrypt communication with your customers.
    * Token Management: Securely generate and transmit tokens to end-users.
    * Data Protection: Encrypt communication between your platform and end-users.
    * Key Rotation: Implement regular key rotation and secure storage practices.

* Shared Responsibilities:
    * Keys: PulseBeam API Keys are asymmetric. We do not store your private keys. Private keys are only available on generation. Store them securely.
    * Key Management: Protect PulseBeam API keys and secrets collaboratively. Rotate keys regularly.
    * PulseBeam Account: Protect your account, only give access to your project(s) with people you trust. People you give access to can delete projects, delete keys, and create keys.

By following these best practices, customers can protect the security of their applications and maintain end-user trust.


![Quickstart Sequence Diagram](../../../../assets/concepts.security.shared.svg)


PulseBeam Responsibilities:

    Infrastructure Security: Ensuring the servers (e.g., signaling servers, media servers) and storage systems are encrypted, secure, and reliable.
    Signaling and Media Encryption:
        Encrypting all communication between our servers and your end users as well as your end users and end users (provided by WebRTC).
    Platform Uptime and Reliability: Maintaining secure and available infrastructure for uninterrupted service meeting SLAs.
    Key Management:
        Safeguarding your accounts and data in our backend.

Your Responsibilities:

    Customer End-User Authentication:
        Authenticating your customers and ensuring only authorized users can access your projects(s).
    Token Management:
        Generating tokens for your end-users securely.
        Transmitting tokens securely to your end-users.
    Data Encryption:
        Encrypting communication between your platform and your end-users.
    Key Management:
        Rotating keys regularly and implementing secure secret storage.
    Customer End-User Data Protection:
        Ensuring their own customers' data is protected in transit and at rest.

Shared Responsibilities:

    Key Management:
        Protecting PulseBeam API keys and secrets (shared responsibility with CPaaS).


## 6.1 PulseBeam Responsibilities

Infrastructure security
Platform encryption
Signaling server security
TURN/STUN server management
Token validation
Access control enforcement

## 6.2 Customer Responsibilities

End-user authentication
Token generation and management
Private key protection
Security policy configuration
Client-side encryption implementation
End-user data protection

## 6.3 Shared Responsibilities

Key rotation practices
Account security
Access management
Incident response
Security monitoring

# 7. Security Checklist

Best Practices and Recommendations

We encourage developers to adopt secure coding practices, especially for applications handling sensitive information. Best practices include:

* Encrypting your traffic using secure encryption protocols.
* Pre-registering users or requiring authentication before allowing participation in connections.
* Clearly communicating permission requests to users to prioritize privacy.

## 7.1 Token Management

Implement short token TTL
Secure token transmission
Regular key rotation
Strict policy configuration

## 7.2 Privacy and User Consent

reccomend to highlight to the user what permission they are giving

## 7.2 Security Implementation

Use HTTPS for all client communications
Implement proper user authentication
Follow secure coding practices
Monitor for suspicious activities

## 9. Conclusion

PulseBeam's security provides a robust foundation to secure real-time communications. PulseBeam leverages WebRTC's security features and supplements them with additional protections to deliver a secure, reliable communication platform.  By following our shared responsibility model and implementing recommended security practices, you can ensure a high level of security for your applications and users.

# Learn more

Contact us for questions

Read more:

https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Connectivity

https://webrtcforthecurious.com/docs/04-securing/

https://web.dev/articles/webrtc-basics#toc-security

https://bloggeek.me/is-webrtc-safe/