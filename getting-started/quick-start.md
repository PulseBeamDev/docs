---
title: Quickstart (5 min)
description: Getting Started with Pulsebeam
---

## What are we going to do?
 
 Create **real-time text editor** between two peers.

1. Account setup. 
1. Run a demo locally. 

Let's get started.

## 1. Your Account

Sign up for a PulseBeam account and get an `api_key` and `api_secret`.

1. Go to [pulsebeam.dev](https://pulsebeam.dev) 
1. Create and account if you don't have one
1. Create a project, name your project
1. Create a keypair on your project, give your keypair a name, e.g. demokey
1. Store those keys!

## 2. Serve

Now that you’ve created your account and obtained your API credentials, let’s set up your local development environment.

You will need `npm` and `node.js` installed on your system. If you don't have them, see <a href="https://docs.npmjs.com/downloading-and-installing-node-js-and-npm" target="_blank">installation instructions</a>

```bash
git clone git@github.com:PulseBeamDev/pulsebeam-js.git
cd pulsebeam-js/demo-cdn
npm i
export PULSEBEAM_API_KEY="kid_..."
export PULSEBEAM_API_SECRET="sk_..."
npm run start
```

## 3. See it
* Go to your browser open two tabs:
    * URL for first tab: <a href="http://localhost:3000/?peerId=peer-29" target="_blank" rel="noreferrer noopener">localhost:3000/?peerId=peer-29</a>
    * URL for second tab:  <a href="http://localhost:3000/" target="_blank" rel="noreferrer noopener">http://localhost:3000/</a>
* On the second tab enter peer-29 in the first text box. Then click connect.
* Type text in the bottom text box. Changes will synchronize between peers in real-time using WebRTC data channels.

<iframe style="height: 480px;" id="ytplayer" type="text/html" title="YouTube video player Quickstart Successful Output" width="2000" height="1000"
src="https://www.youtube.com/embed/Y9mKCrlLu7k?si=HDNMFjK6LaXSy61X?loop=1&modestbranding=1&playsinline=1&color=white&iv_load_policy=3"
frameborder="0" allowfullscreen allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin"></iframe>

## Congratulations! 

You’ve set up a real-time peer-to-peer connection using PulseBeam. Checkout 'what happened' in the next page. Explore the logs, experiment with the demo, and take the next step by using PulseBeam in your applications.

## Troubleshooting
* Check your environment variables with `echo $PULSEBEAM_API_KEY && echo $PULSEBEAM_API_SECRET`
* Port 3000 is already in use (`Error: listen EADDRINUSE: address already in use :::3000`) close other process or change port.
* Something odd? Check you node version with `node -v` and `npm -v`. If older than 23.5.0 & 11.0.0 try `nvm use node` or `nvm use 23.5`
* Something else? Checkout logs in browser console or [contact us](/docs/community-and-support/discord/)!