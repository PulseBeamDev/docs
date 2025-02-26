---
title: Quickstart (5 minutes)
description: Getting Started with Pulsebeam
tableOfContents: { minHeadingLevel: 1, maxHeadingLevel: 4 }
---

:::note

This quickstart includes creating a PulseBeam Account, Project, and Key. 

* See a list of [our plans](https://pulsebeam.dev/#pricing).
* For terms you don't understand, see [our glossary](/docs/concepts/terms).

:::

## What are we going to do?
 
Create **real-time text editor** between two peers.

1. Account setup. 
1. Run a demo locally. 

Let's get started.

## 1. Your Account

Sign up for a PulseBeam account and get an `api_key` and `api_secret`.

1. Go to [cloud.pulsebeam.dev](https://cloud.pulsebeam.dev) 
1. Login / create and account
1. Create a project, name your project. 
    * Note: Plans are scoped to a project. Your project is Sandbox (free) by default until you change it. [Contact us](/docs/community-and-support/support/) to change it.
1. Create a keypair on your project, give your keypair a name, e.g. demokey
1. Store those keys! You will not be able to access the private key again (but if you lose it or want to rotate, feel free to delete and recreate keys)

## 2. Serve it

Now that you’ve created your account and obtained your keys, let’s set up your local development environment.

You will need `npm` and `node.js` installed on your system. If you don't have them, see <a href="https://docs.npmjs.com/downloading-and-installing-node-js-and-npm" target="_blank">their installation instructions</a>

Pull down the project:
```bash
git clone git@github.com:PulseBeamDev/pulsebeam-js.git
cd pulsebeam-js/demo-cdn
```

Open `index.html` in your editor of choice and replace these lines with your key:
```javascript
apiKey: "kid_<...>",
apiSecret: "sk_<...>",
```

Run demo locally with:
```bash
npm i
npm run start
```

You should now have a server running locally on port 3000

## 3. See it

* Go to your browser open two tabs:
    * URL for first tab: <a href="http://localhost:3000/?development=true&peerId=peer-29" target="_blank" rel="noreferrer noopener">localhost:3000/?development=true&peerId=peer-29</a>
    * URL for second tab: <a href="http://localhost:3000/?development=true" target="_blank" rel="noreferrer noopener">localhost:3000/?development=true</a>
* On the second tab enter peer-29 in the first text box. Then click connect.
* Type text in the bottom text box in either tab. Changes will synchronize between peers in real-time using WebRTC data channels.

Here's what you should see:
<iframe style="height: 480px;" id="ytplayer" type="text/html" title="YouTube video player Quickstart Successful Output" width="2000" height="1000"
src="https://www.youtube.com/embed/Y9mKCrlLu7k?si=HDNMFjK6LaXSy61X?loop=1&modestbranding=1&playsinline=1&color=white&iv_load_policy=3"
frameborder="0" allowfullscreen allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin"></iframe>

## Congratulations! 

You’ve set up a real-time peer-to-peer connection using PulseBeam. 

Next steps:
* [Learn: What happened?](/docs/getting-started/what-happened/). 
* Explore the logs
* Experiment with the demo

:::danger

Earlier in the demo, we embedded our key in `index.html`

That was unsafe. It was for demo and development purposes only. Under no circumstances should you publish your private key in your client

We have a guide on [serving tokens in production](/docs/guides/token/).
:::

## Other Demos

### Google Meet Clone

Check it out live: https://meet.pulsebeam.dev/

Read the source: https://github.com/PulseBeamDev/pulsebeam-js/tree/main/demo-react

## Troubleshooting
* Check browser console, network tab, make sure your application received a valid auth token. Your request/response should look something like:
    ```json
    {
        "request": {
            "method": "POST",
            "url": "https://cloud.pulsebeam.dev/sandbox/token",
        },
        "response": {
            "status": 200,
            "content": {
                "text": "eyJhbGciOiJFZERTQSIsImtpZCI6ImtpZF8wZGZhNTY5YjEwODUwYTVhIn0.eyJleHAiOjE3Mzg3OTQzMTMsImdpZCI6ImRlZmF1bHQiLCJwaWQiOiJwZWVyLTg2IiwiYXAiOnsiZ3JvdXBfaWQiOiIqIiwicGVlcl9pZCI6IioifX0.MOdiTUebb_n-WiAV8_2UiaA2YO8JvenQ0J9wkU7_wEtVHli_ySTM51u4SHMtpjgkjHuY70OjPrGpM-Daj7UMAg"
            },
        },
    },
    ```
* If your response is something like: `Status code 500` `Something went wrong: failed to create sandbox token` Check your keys in `index.html`
* The token is the text in the response (example above). You can decode it to inspect further. When you decode it, you should see something like:
    ```json
    {
        "header": {
            "alg": "EdDSA",
            "kid": "kid_0dfa569b10850a5a"
        },
        "payload": {
            "exp": 1738794313,
            "gid": "default",
            "pid": "peer-86",
            "ap": {
                "group_id": "*",
                "peer_id": "*"
            }
        }
    }
    ```
    Note: Your decoder may throw an "invalid signature" error when it does not support the algorithm of the token. We currently use EdDSA, which is fairly new (and well regarded).
* Port 3000 is already in use (`Error: listen EADDRINUSE: address already in use :::3000`) close other process or change port.
* Something odd? Check you node version with `node -v` and `npm -v`. If older than 23.5.0 & 11.0.0 try `nvm use node` or `nvm use 23.5`
* Something else? Checkout logs in browser console or [contact us](/docs/community-and-support/support/)!
