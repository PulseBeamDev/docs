---
title: Connection
description: Understanding connections and lifecycle
tableOfContents: { minHeadingLevel: 1, maxHeadingLevel: 4 }
---

PulseBeam simplifies WebRTC connections, managing state, reconnections, and common edge cases. By abstracting the complexity, establishing a connection looks like:

```js
peer.start();  
peer.connect(otherPeer , ...);  
```

# Abstractions

### Session, Connection, and Peer Explained:

Peer:
* Represents your local entity.
* Manages authentication (via token), connection settings, and can have multiple Sessions.
* Can initiate connections to other peers via `peer.connect(...)`.

Session:
* A single connection to another peer, wrapping an RTCPeerConnection.
* Handles WebRTC negotiation (offers/answers), ICE candidates, media tracks, and data channels.
* Equivalent to a "connection" in this context – one Session per remote peer.

Connection:
* Synonymous with a Session in this abstraction. Each Session represents a bi-directional connection to another peer.

### Key Capabilities:

Multiple Peers: A single Peer can have multiple Sessions (connections) with different remote peers simultaneously.
```ts
// Peer can connect to multiple peers
await peer.connect(group1, "peerA", signal1);
await peer.connect(group1, "peerB", signal2);
```
Data Channels: Each Session supports multiple data channels:
```ts
// Create multiple data channels per Session
const chatChannel = session.createDataChannel("chat");
const gameDataChannel = session.createDataChannel("game-data");
```
Sessions vs Connections:

* 1 Session = 1 Connection to 1 peer. Sessions are not reused between peers.

For Example, let's say
* Alice is a peer
* Bob is a peer
* John is a peer
* If Bob connects to Alice and Bob connects to John, Bob will have two distinct sessions


Flow Diagram:
```
Peer
│
├── Session 1 (Connection to Peer A)
│   ├── Data Channel 1
│   └── Data Channel 2
│
└── Session 2 (Connection to Peer B)
    └── Data Channel 1
```

# Connection State

To build responsive UIs that adapt to dynamic network, connection, and state conditions, you need to know the connection state. Specifically, the **RTC Peer Connection State** describes this connection state, representing the underlying WebRTC Peer connection state.
 
### Accessing state

In a web application using `@pulsebeam/peer` SDK, open you app, open browser console, make sure logs are verbose, create a connection, search for `connectionstate`. 

To test various network condition, create and manipulate the state, you can turn on airplane mode for various amounts of time, closing tabs, setting packet loss on your system, and much more, experiment! 

You can listen to underlying WebRTC connection state change with callback `onconnectionstatechange` or get current state `connectionstate()`:

```js
peer.onsession = (session) => {
  session.onconnectionstatechange = (state) => console.log(`WebRTC Connection state changed to: ${state}`);
  console.log(`The WebRTC connection state right now is: ${session.connectionstate()}`);
};
```

### Understanding the State

![Connection state flowchart](../../../../assets/concepts.connection.svg)

:::note
This flowchart provides a high-level overview of typical behavior. It is meant to help you understand and conceptualize states and their meaning. WebRTC connections can exhibit unexpected transitions and/or flakiness. 
:::

Peer SDK insulates you from some flakiness in connection state, with shimming, polyfills, and auto retries on top of existing browser WebRTC implementation. As well as handling reconnection in recoverable states.

If you have any questions or comments, [chat with us](/docs/community-and-support/discord)!

#### Expected State Transition Flows

Here are some expected UI flow snip bits. Here are how you should think about isolated sections of the connection state

**Connection Establishment Happy Path**

`new → connecting → connected`

**Broken Connection to Auto Recovery**

`connected → disconnected (auto retry loop) → connecting → connected`

**Broken Connection to Unrecoverable Detected**

`connected → disconnected (auto retry loop) → failed (max retry or timeout reached)  → closed (automatically closed due to failed state)`

**Connection to Close Initiated by a Peer**

`connected → closed (peer.close())`


#### Transitions and Flows in Your Application

Transitions should be smooth to avoid jarring UX, with clear differentiation between temporary (e.g., disconnected) and permanent (e.g., failed) states.

**Example State-to-UI Summary Table**

While your UI/UX will be highly application-dependent. The table below is meant to be as a learning resource and potentially a starting place. For fleshing out and understanding your UI with WebRTC.

| **Connection State** | **Suggested UI Elements**                     | **Desired User Action**                 |
|----------------------|-----------------------------------------------|-----------------------------------------|
| `new`                | Loading spinner, "Setting up connection..."   | Wait for connection                     |
| `connecting`         | Loading spinner, "Connecting to peers..."     | Wait for connection                     |
| `connected`          | Green badge, full experience enable           | Access and interact with application    |
| `disconnected`       | Loading spinner, "Attempting to reconnect..." | Wait for reconnection or retry options  |
| `failed`             | Error message, troubleshooting options        | Retry button, guide for troubleshooting |
| `closed`             | Confirmation message                          | Option to restart connection            |

**Best Practices**

* Transitions: Smoothly animate transitions between states to avoid jarring experiences.
* Error Handling: Clearly differentiate between temporary (disconnected) and permanent (failed) issues.
* Feedback Loop: Use tooltips or messages to keep users informed, particularly during connecting and disconnected states.
* Accessibility: Use visual indicators (e.g., colors/icons) alongside textual cues for universal understanding.
* Timeouts: Consider handling long connecting or disconnected states gracefully, e.g., by prompting the user after a timeout.

By carefully mapping connection states to user-friendly labels and actionable UI elements, you ensure a polished experience that minimizes confusion and frustration for your users.

We wish you the best and would love to chat and see what you build, [connect with us](/docs/community-and-support/discord)!

# Token State

Tokens govern user access to PulseBeam's platform. Including session lifetime. 

You define token TTL (expiry), see [Security](/docs/concepts/security-and-architecture/) for more information on TTL.

When a token expires and the user wants to maintain a connection, you should recreate peers and connections.


**Scenarios to Consider:**

* **Token Expiration During Active Session**: If a token expires mid-session, the connection will terminate. Proactively monitor and refresh tokens to prevent disruptions.

* **Session Expiry:** Upon session expiration, invalidate the peer and establish a new connection post-refresh.

**Best Practices:**

* Monitor token TTL and current time
* Reinitialize peers upon token refresh.
* Handle reconnections gracefully during token transitions to avoid disrupting user experiences.

**What happens when tokens expire during an active session?**

The connection will be closed

**PulseBeam's Next Steps**

We would love to change this in the future. Likely next improvement would be to add a demo and sample code here. Then next would be to add hooks for token refresh.

If there is enough interest from our community. So [share your thoughts!](/docs/community-and-support/discord)

# PulseBeam Peer State

For use cases including token invalidation, you can use **PulseBeam Peer State** defined in PulseBeam Peer JS SDK.

**PulseBeam Peer State**

You can listen to the Peer's state change with a callback `onstatechange`, get current state with `state()`, and transition to closed with `close()`:

```js
peer.onstatechange = (state) => {
    console.log(`The peer state changed to: ${state}`);
}
console.log(`The peer's state is: ${peer.state()}`)
```

**PulseBeam Peer State**

The PulseBeam State is `new` or `closed`. When the state is `closed` the Peer must be recreated to transition back to `new`. State must be `new` to be able to make connections.

# Code Samples

* Video https://meet.pulsebeam.dev/ `demo-react`, video call app
* Data channel `demo-cdn`, our [quickstart demo](/docs/getting-started/quick-start) - [code](https://github.com/PulseBeamDev/pulsebeam-js/tree/main/demo-cdn)

# Learn More

By understanding state and leveraging PulseBeam's abstractions, you can build robust WebRTC applications with intuitive UI/UX and reliable connection management.

* [WebRTC: `connectionState` Documentation](https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/connectionState)

* [WebRTC: Connection lifecycle, from establishment to closure. 'Why' behind the steps. Documentation](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Session_lifetime)

* [WebRTC: Connection establishment, technical and detailed. 'How' behind the steps. Documentation](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Connectivity)