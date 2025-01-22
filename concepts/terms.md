---
title: Terms
description: Getting to know terms and glossary
tableOfContents: { minHeadingLevel: 1, maxHeadingLevel: 4 }
---

### YOU/YOUR

Refers to the intended reader, developers using PulseBeam CPaaS. You log in to the admin console and get keys to write web applications. You use `@pulsebeam/server` and `@pulsebeam/peer` SDKs to create applications.

### PULSEBEAM SDK CLIENT

`@pulsebeam/peer` JS Client SDK for creating WebRTC connections with PulseBeam from web clients [see SDK Docs](/docs/reference/peer-js)

### PULSEBEAM SDK SERVER

`@pulsebeam/server` SDK for generating tokens to allow your clients to use your PulseBeam Project [see SDK Docs](/docs/reference/server-js)

### APPLICATION/PROJECT

the app you are creating with PulseBeam. For project creation see admin console and [Quickstart](/docs/getting-started/quick-start). For project concepts see [Project Access](/docs/concepts/security-and-architecture/#project-access-control)

### PEER/CUSTOMER/USER/END-USER

Your user. E.g. Alice and Bob the intended audience for your application

### WE/US/PULSEBEAM

PulseBeam, us, the CPaaS provider

### KEYS

Key pair(s) for your PulseBeam project [see key docs](docs/concepts/security-and-architecture/#pulsebeam-project-keys)

### SECRETS/PRIVATE KEYS

The private key in the asymmetric key set provided by PulseBeam to you

### CONNECT/CONNECTION

The link created by PulseBeam between Alice and Bob. This link is for secure data transfer in real-time. It is a peer-to-peer WebRTC connection.

### REAL-TIME

End-to-end latency of data sent over the connection will usually be 50-200ms (Same as telephone call latency)(which is sufficient to be perceived by human users as real-time). This is achieved through various [WebRTC protocols](/docs/concepts/webrtc/#communicating-with-peers-via-rtp-and-sctp) which allows your software and projects to connect human and/or non-human systems, perform functions, and can respond to events within predictable and specific time constraints, time frame, or deadline. Latency can be higher, as networks and distances between peers are variable.

### PEER-TO-PEER

Refers to the targets of the connection and the path of data flow over the connection. Data flows directly between two peers over the internet. All data is encrypted, so only the two peers can read it.

### WEBRTC/WEBRTC AGENT

WebRTC, short for Web Real-Time Communication, defines a protocol for two WebRTC agents to negotiate bidirectional secure real-time communication connection. Learn more [here](/docs/concepts/webrtc)

### SIGNALING

When Alice goes to their browser, WebRTC Agent in the browser, has no idea who it is going to communicate with or what they are going to communicate about. Signaling performs this discovery, bootstrapping the connection. Learn more about [signaling in WebRTC](/docs/concepts/webrtc/#signaling-how-peers-find-each-other-in-webrtc) and [PulseBeam's signaling security](/docs/concepts/security-and-architecture/#signaling-security)

### STUN

In order to connect the peers, Signaling needs to know the address of the peers, but because of networking infrastructure, often peers don't know their own address. STUN servers exist to echo back a peer's address to itself. Read more about STUN [here](/docs/concepts/security-and-architecture/#turn--stun-security) and [here](/docs/concepts/webrtc/#connecting-and-nat-traversal-with-stunturn)

### TURN/RELAY

In some cases, due to one or both peer's network firewalls and configuration, in order to make a connection between the peers, data will be forced to flow through an intermediary server. We refer to this server as a TURN server, and call it a relay. Read more about TURN [here](/docs/concepts/security-and-architecture/#turn--stun-security) and [here](/docs/concepts/webrtc/#connecting-and-nat-traversal-with-stunturn). You can [force TURN](https://jsr.io/@pulsebeam/peer/doc/~/PeerOptionsFull#property_forcerelay) over all connections.

### TOKEN

PulseBeam's access control system. Issued by you, for Alice and Bob to use PulseBeam infrastructure and resources. Read more about [security concepts](/docs/concepts/security-and-architecture). See [PulseBeam Server SDK](/docs/reference/server-js) and [PulseBeam CLI](/docs/reference/cli) for generating tokens.

### POLICY

`PeerPolicy` defines which peers this peer can connect to. You define the security policy on the token. We enforce policies that you set on the token. Learn about [concepts here](/docs/concepts/security-and-architecture/#customer-tokens-ttl-policies) and format and setting policies [here](https://jsr.io/@pulsebeam/server/doc/~/PeerPolicy).

### PULSEBEAM CLI

CLI vended by PulseBeam to allow you to generate tokens from your key without hosting a server. [See docs](/docs/reference/cli)

## We are missing something? [Contact us](/docs/community-and-support/discord)