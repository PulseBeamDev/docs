---
title: Terms
description: Getting to know terms and glossary
tableOfContents: { minHeadingLevel: 1, maxHeadingLevel: 4 }
---

### YOU/YOUR

Refers to the intended reader, developers using PulseBeam CPaaS. You log in to the [admin console](https://cloud.pulsebeam.dev/) and get [keys](/docs/concepts/terms#keys) to create [applications](/docs/concepts/terms#applicationproject). You use `@pulsebeam/server` and `@pulsebeam/peer` SDKs to create applications.

### PEER/CUSTOMER/USER/END-USER

Your user. E.g. Alice and Bob the intended audience for your application

### WE/US/PULSEBEAM

PulseBeam, us, the CPaaS provider

### PULSEBEAM SDK CLIENT

`@pulsebeam/peer` JS Client SDK for creating [WebRTC](/docs/concepts/terms#webrtcwebrtc-agent) connections with PulseBeam from web clients [see SDK Docs](/docs/reference/peer-js)

### PULSEBEAM SDK SERVER

`@pulsebeam/server` SDK for generating [tokens](/docs/concepts/terms#token) to allow your clients to use your [PulseBeam Project](/docs/concepts/terms#applicationproject) [see SDK Docs](/docs/reference/server-js)

### APPLICATION/PROJECT

The app you are creating with PulseBeam. For project creation see [admin console](https://cloud.pulsebeam.dev/) and [Quickstart](/docs/getting-started/quick-start). For more on project concepts [see here](/docs/concepts/security-and-architecture/#project-access-control)

### KEYS

Key pair(s) for your PulseBeam project. Key pairs are for paid plan users to provide peers access to PulseBeam Cloud. [To learn more, see key docs](/docs/concepts/security-and-architecture/#pulsebeam-project-keys)

### SECRETS/PRIVATE KEYS

The private key in the asymmetric key set provided by PulseBeam to you.

### CONNECT/CONNECTION

The link created by PulseBeam between Alice and Bob. This link is for secure data transfer in real-time. It is a peer-to-peer WebRTC connection.

### REAL-TIME

End-to-end latency of data sent over the connection will usually be 50-200ms (Same as telephone call latency)(which is sufficient to be perceived by human users as real-time). 

This is achieved through various [WebRTC protocols](/docs/concepts/webrtc/#communicating-with-peers-via-rtp-and-sctp) which allows your software and projects to connect human and/or non-human systems, perform functions, and can respond to events within predictable and specific time constraints, time frame, or deadline. Latency can be higher, as networks and distances between peers are variable.

### PEER-TO-PEER

Refers to the targets of the connection and the path of data flow over the connection. Data flows directly between two peers over the internet. All data is encrypted, so only the two peers can read it.

### WEBRTC/WEBRTC AGENT

WebRTC, short for Web Real-Time Communication, defines a protocol for two WebRTC agents to negotiate a bidirectional secure real-time communication connection. Learn more [here](/docs/concepts/webrtc)

### SIGNALING

When Alice goes to their browser, the WebRTC Agent in the browser, has no idea who it is going to communicate with. Signaling performs this discovery, bootstrapping the connection. Learn more about [signaling in WebRTC](/docs/concepts/webrtc/#signaling-how-peers-find-each-other-in-webrtc) and [PulseBeam's signaling security](/docs/concepts/security-and-architecture/#signaling-security)

### STUN

In order to connect the peers, Signaling needs to know the address (IP) of the peers, but because of networking infrastructure, often peers don't know their own address. STUN servers echo back a peer's address to itself. Read more about STUN [here](/docs/concepts/security-and-architecture/#turn--stun-security) and [here](/docs/concepts/webrtc/#connecting-and-nat-traversal-with-stunturn)

### TURN/RELAY

In some cases, due to one or both peer's network firewalls and configuration, in order to make a connection between the peers, data will be forced to flow through an intermediary server. We refer to this server as a TURN server, and call it a relay. Read more about TURN [here](/docs/concepts/security-and-architecture/#turn--stun-security) and [here](/docs/concepts/webrtc/#connecting-and-nat-traversal-with-stunturn). You can [force TURN](https://jsr.io/@pulsebeam/peer/doc/~/PeerOptionsFull#property_forcerelay) over all connections.

### TOKEN

PulseBeam's access control system. Issued by you, for Alice and Bob to use PulseBeam infrastructure and resources. 

For more security concepts, [go here](/docs/concepts/security-and-architecture). 

To generate tokens, you need a [Key](/docs/concepts/terms#keys). For generating tokens, see [PulseBeam Server SDK](/docs/reference/server-js) and [PulseBeam CLI](/docs/reference/cli).

### POLICY

`PeerPolicy` defines which peers this peer can connect to. You define the security policy on the token. We enforce policies that you set on the [token](/docs/concepts/terms#token). Learn about [concepts here](/docs/concepts/security-and-architecture/#customer-tokens-ttl-policies) and format and setting policies [here](https://jsr.io/@pulsebeam/server/doc/~/PeerPolicy).

### PULSEBEAM CLI

CLI vended by PulseBeam to allow you to generate [tokens](/docs/concepts/terms#token) without hosting a server. [Read CLI docs](/docs/reference/cli)

### PULSEBEAM CLOUD / PULSEBEAM SERVER

Refers to PulseBeam's infrastructure / platform that is intended to be used by your [project](/docs/concepts/terms#applicationproject). This includes [Signaling](/docs/concepts/terms#signaling), [STUN](/docs/concepts/terms#stun), and [TURN](/docs/concepts/terms#turnrelay) servers / services. 

[PulseBeam Admin Dashboard](https://cloud.pulsebeam.dev/) allows you to create [PulseBeam Project(s)](/docs/concepts/terms#applicationproject) and [key(s)](/docs/concepts/terms#keys) to access PulseBeam Cloud.

### SELF-HOST

Refers to the [open-source signaling server](https://github.com/PulseBeamDev/pulsebeam-server-foss) that we provide. You can use this in place of [PulseBeam Cloud](/docs/concepts/terms#pulsebeam-cloud--pulsebeam-server). We use this server in PulseBeam Cloud, so you can be sure it is working and maintained.

## We are missing something? [Contact us](/docs/community-and-support/discord)