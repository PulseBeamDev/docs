---
title: Quickstart Review
description: Review of concepts and what happened in Quickstart
tableOfContents: { minHeadingLevel: 1, maxHeadingLevel: 4 }
---

## What happened in our Quickstart?

### 1. You setup your local environment

* Pulled down code
* Built project
* Set your key

### 2. Started a Server

* `npm start` spun up the HTTP server (`localhost:3000`)

```
HTTP Server (localhost:3000)
            └── / -> index.html - frontend code
```

### 3. Experienced a Token Request Workflow

* You opened your browser, provided URL `localhost:3000`, which loaded `index.html`.
* `index.html` instructs browser to make a request to `https://cloud.pulsebeam.dev/sandbox/token` (lets refer to this as `/token` for short) for a token using the key you provided
```
Browser → GET / -> index.html → Request Token from /token
```

### 4. Created tokens

* `/token` uses `@pulsebeam/server` SDK with your key to generate tokens.

```
/token → @pulsebeam/server SDK → Token → Browser
```

### 5. Started peers

* `index.html` uses `@pulsebeam/peer` SDK with the token to create a peer and communicate with PulseBeam signaling servers.

```
Token → @pulsebeam/peer SDK peer.start() → PulseBeam Signaling Servers
```

### 6. Established a Peer-to-Peer Connection

* Second tab enters `peer-29` in the textbox and clicks `Connect`.
* The second browser initiates a connection to the first browser.
* PulseBeam established a connection.
* Note: `index.html` defines application logic: create data channel and sync text in real time
```
peer → peer.connect('peer-29') → PulseBeam Signaling Servers → connected
```

### Summary

![Quickstart Sequence Diagram](../../../../assets/getting-started.quick-start.seq.svg)

## Tips 

Logs can be viewed from your console browser inspector.

PulseBeam will work globally within the scope of your project.
* Peers are not limited to two tabs within the same browser.
* You can establish a connection between peers on different networks or machines.

We can't wait to see what you build on PulseBeam! [Connect on Discord](/docs/community-and-support/discord/)!

# Next Steps

* The way we received tokens in the demo (by embedding our private key in our client) was 100% insecure and NOT production ready!! See our guide on [serving tokens in production](/docs/guides/token/)
* To get some bonus points, see [below](#bonus-points) 
* Dive into our [Concepts](/docs/concepts/terms)
* Use PulseBeam in your applications 😊

## Bonus points

Experiment with the demo!

Update frontend to work with [PulseBeam CLI](/docs/reference/cli/) instead of `/auth` for token generation. 
    <details>
    <summary>Hint: </summary>
    One way is to create another input for <code>token</code>, and add a <code>peer.start()</code> button.
    </details>
