---
title: 'What is PulseBeam?'
description: Overview of PulseBeam
tableOfContents: { minHeadingLevel: 1, maxHeadingLevel: 4 }
---

## TL;DR: Open-Source WebRTC platform built for Engineers

**Full native WebRTC control without the upfront investment, maintenance, and ongoing complexity.**

* ⚡ **Peer-to-peer first** avoid giving your data to the middlemen
* 🔒 **End-to-end encrypted** media + signaling + auth controls
* 🪄 **Automatic reconnection** + edge-case handling
* 🔌 **Free** signaling servers & protocol. Self-host or use our ☁️
* 🚀 **Production-ready SDK** own less than 100 lines of code
* 🛠️ **Actively maintained** OSS stack (no abandoned projects)
* 🌐 **Global STUN/TURN network** free STUN, usage-based TURN

## 💡 What WebRTC Infrastructure Problem?

WebRTC is powerful! Building and maintaining the required infrastructure is complex, time-consuming, and expensive.

|Challenge	|In-House Approach	|PulseBeam Solution|
|---|---|---|
|Signaling servers	|Build custom protocol + manage scaling	|Prebuilt secure protocol + free hosted servers|
|STUN/TURN setup	|Deploy and manage global servers	|Global network (STUN free, TURN pay-as-you-go)|
|Security audits	|Time-consuming	|E2E encryption + auth controls built-in|
|Edge-case handling	|Manual testing for reconnections/NAT issues	|Automatic recovery in SDK|

**Hidden Costs of In-House WebRTC**

* Debugging: Connections happen in clients, debugging requires deep knowledge, patience, and building in visibility.
* Maintenance: Protocol updates, server scaling, security patches
* TURN costs: Requires multiple regions for low-latency, leading to overprovisioning
* Developer time: Months spent debugging edge cases instead of building features

## 1. 🔒 Security Built In

While WebRTC mandates encryption of data and media between peers, "Signaling mechanisms aren't defined by WebRTC standards, so it's up to you to make signaling secure. If an attacker manages to hijack signaling, they can stop sessions, **redirect connections, and record, alter, or inject content**." — [Sam Dutton](https://web.dev/articles/webrtc-infrastructure), Google Chrome Developer Advocate 

Security is a top priority at PulseBeam. The highlights:

* **End-to-end encryption**: All media and data transfers are secure
* **Token-based access with authorization**: Your policies define which peers can connect within your app.
* **Mandatory HTTPS**: Encryption for all signaling traffic
* **Time-bound sessions**: Issue time-limited (TTL) tokens to users, minimize exposure from leaked credentials ensuring secure access to your app.
* **TURN relay immunity**: Our servers can't decrypt your data, even when relaying data
* **Key rotation**: We'll remind you to rotate credentials

Applications are only as secure as their weakest link, WebRTC's encryption does not help if your signaling layer is vulnerable. We designed PulseBeam to be secure by default.

## 2. 💣 Cut out the Middleman

At a fundamental level. Many CPaaS force traffic to flow through their SFU infrastructure. Which brings with it:

* 🔓 **Reduced Data Privacy**: a man in the middle on all data transfer which normally decrypts your stream
* 💰 **Cost**: you are paying their data ingress and egress
* 🐌 **Latency** forced hops through their infrastructure can increase latency significantly depending on your peer location

Non-ideal in many cases. PulseBeam enables direct peer to peer data flow.

## 3. ⌨️ Developer-Centric Control

We expose media streams to give you the tools to build freely. Our philosophy is you own your data. You control your media.

**Hook into media streams to provide:**
* Real-time captions
* Pattern recognition
* Classification
* Accessories on face landmark
* Gesture recognition
* Many more!

We want you to have access to the raw streams and media. So we expose the underlying media tracks and browser APIs in our SDKs. 

**Access Raw Media Streams:**
```javascript
// Hook into local stream
const s = await navigator.mediaDevices.getUserMedia({ video: true });
peer.setLocalStream(s);

// Hook into remote stream
peer.onsession = (session) => {
  session.ontrack = ({ streams }) => console.log("New media stream:", streams);
}
```

For browser clients, if your user has the compute, you can use AI Edge models built-into the browser.

## 5. 📫 Signaling 

#### Is Signaling hard?

For production applications to survive, signaling takes some serious consideration. Daniel Petersson, a Google Duo[^1] Engineer has a great [talk about building scalable signaling](https://www.youtube.com/watch?v=nPnWIuAlphc) for a WebRTC production service.

PulseBeam was designed for production applications at scale and to operate for your clients on unreliable networks for fast connections and reconnections.

#### Other open-source signaling?

"Selecting the wrong signaling framework [is a common WebRTC mistake]. PeerJS is a great signaling solution from years ago, [but it's inactive], and I wouldn't use it." - Tsahi Levent-Levi, WebRTC Expert ([Watch his Conference Talk](https://youtu.be/AkzgwpsYrHY?t=1052)). 

Other open-source options, like simple-peer, are similarly inactive. Normally 1-4+ Years between updates.

PulseBeam is actively developed, with regular updates, security patches, full-time team, and dedicated engineering support.

|Feature |	PeerJS	|PulseBeam|
|---|---|---|
|JS Client  | ✅ | ✅ |
|Open Source Self-Host Signaling Server | ✅ | ✅ |
|Free Cloud-Hosted Signaling Server | ✅ | ✅ |
|Last Update|	2023 |	Weekly Commits|
|Commit History	|[PeerJS](https://github.com/peers/peerjs/graphs/code-frequency)	|[PulseBeam](https://github.com/orgs/PulseBeamDev/repositories)|
|Maintenance	|❌ Inactive	|✅ Actively maintained|
|Security	|❌ Insecure	|✅ End-to-end encryption + BYO auth|
|Scalability	|❌ Limited	|✅ Built for production|
|Security Fixes	|`¯\_(ツ)_/¯` Dependency Bot|	Critical Patches <24hr|
|STUN	|Not provided	| Unlimited Free STUN |
|TURN	|None provided	| Usage-based TURN|

## 📊 Why Developers Choose PulseBeam

**You value:**

* **Speed**: Ship WebRTC apps in hours, not months.
* **Control**: Open-source stack with optional managed infrastructure.
* **Cost Efficiency**: No upfront investment; no pay by the minute; scale TURN usage as needed.

**Ideal For:**

When communications is not your core:

* Startups avoiding infrastructure debt
* Engineers prioritizing security/encryption
* Teams building real-time audio/video/data apps

# 👋 Join the PulseBeam Community

While PulseBeam is still early days, we're creating a community of developers building the future of real-time communication. Whether you’re creating a video chat app, a live collaboration tool, or a multiplayer game, PulseBeam has your back.

We're here to keep you productive for the long haul. [Join our Discord](/docs/community-and-support/discord/) if you need help, want to chat, or are thinking of a new feature. We're here to help - and to make PulseBeam even better.

---
[^1]: Duo has since been merged into Google Meet