---
title: Quickstart (5 min)
description: Getting Started with Pulsebeam
---

Quickstart. Account setup, backend, and frontend. For WebRTC peer to peer data channel using PulseBeam. Let's get down to it.

# 1. Your Account
Sign up for a PulseBeam account to get an `api_key` and `api_secret`

# 2. Serve

You will need `npm` and `node.js` installed [see here for installation instructions](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)

```bash
git clone git@github.com:PulseBeamDev/pulsebeam-js.git
cd pulsebeam-js/demo-cdn
npm i
export PULSEBEAM_API_KEY="my_api_key"
export PULSEBEAM_API_SECRET="my_api_secret"
npm run start
```

# 3. See it
* Go to your browser open two tabs:
    * URL for first tab: http://localhost:3000/?peerId=peer-29
    * URL for second tab: http://localhost:3000/
* On the second tab say peer-29 in the first text box. Then click connect.
* You can edit the text in the bottom text box. And see it synchronizing between the peers.