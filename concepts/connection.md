---
title: Connection
description: Understanding connections and lifecycle
---

PulseBeam simplifies WebRTC connections, managing state, reconnections, and common edge cases. By abstracting the complexity, establishing a connection looks like:

```js
peer.start();  
peer.connect(otherPeer);  
```

## Connection State

To build responsive UIs that adapt to dynamic network, connection, and state conditions, you likely want to know the connection state.

There are two kinds of state to be aware of: 
* **PulseBeam Peer State** - JS SDK Peer object's state
* **RTCPeerConnectionState** - Underlying WebRTC Peer connection state
 
To learn about these states keep reading!

### Accessing the state

**PulseBeam Peer State**

You can listen to PulseBeam's peer's state change with a callback `onstatechange` or get current state with `state()`:

```js
peer.onstatechange = (state) => {
    console.log(`The peer state changed to: ${state}`);
}
console.log(`The peer's state is: ${peer.state()}`)
```

**RTCPeerConnectionState**

Similarly, you can listen to underlying WebRTC connection state change with callback `onconnectionstatechange` or get current state `connectionstate()`:

```js
peer.onsession = (session) => {
  session.onconnectionstatechange = (state) => console.log(`WebRTC Connection state changed to: ${state}`);
  console.log(`The WebRTC connection state right now is: ${session.connectionstate()}`);
};
```

### Understanding the State

**PulseBeam Peer State**

The PulseBeam State is straightforward. State is `new` or `closed`. When the state is `closed` peers must be recreated to transition back to `new`. State must be `new` to be able to make connections.

**RTCPeerConnectionState**

![Connection state flowchart](../../../../assets/concepts.connection.svg)

Note: This flowchart provides a high-level overview of typical behavior. It is meant to help you understand and conceptualize states and their meaning. WebRTC connections can exhibit unexpected transitions and/or flakiness. 

Peer SDK insulates you from some flakiness in connection state, with shimming, polyfills, and auto retries on top of existing browser WebRTC implementation. Handling reconnection in recoverable states.

If you have any comments or would like to [chat with us](/docs/community-and-support/discord)!

#### Expected State Transition Flows

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

## Handling Token TTL

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

We would love to change this in the future. Likely next improvement would be to add hooks for token refresh.

TODO: Add a demo and sample code here.

If there is enough interest from our community. So [share your thoughts!](/docs/community-and-support/discord)

# Code Samples

* [React Demo](https://github.com/PulseBeamDev/pulsebeam-js/tree/main/demo-react)

# Learn More

By understanding state and leveraging PulseBeam's abstractions, you can build robust WebRTC applications with intuitive UI/UX and reliable connection management.

* [WebRTC: `connectionState` Documentation](https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/connectionState)

* [WebRTC: Connection lifecycle, from establishment to closure. 'Why' behind the steps. Documentation](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Session_lifetime)

* [WebRTC: Connection establishment, technical and detailed. 'How' behind the steps. Documentation](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Connectivity)