---
title: Serving Tokens
description: Getting Tokens in Production with Pulsebeam
tableOfContents: { minHeadingLevel: 1, maxHeadingLevel: 4 }
---

This guide provides production-ready strategies to securely serve PulseBeam tokens to your users. Choose the method that aligns with your infrastructure and platform needs.

## Why do I want to serve tokens?

Going to production will require you to serve tokens to your users. Tokens are what allow your users to access PulseBeam and are essential for security and auth.

To learn more about unfamiliar [terms](/docs/concepts/terms)

To learn more about security concepts, checkout our [security concepts](/docs/concepts/security-and-architecture)

## How to serve tokens?

In our quickstart, we have learned one way to get tokens to your users:
1. Publish your project's secret key into your client (absolutely insecure, you should not expose your secret key in your clients, this is for development only!!!)

There are several other ways to get tokens:
1. Serve in a node / deno environment with our `@pulsebeam/server` package. You can host this however you like
1. Serve in a Cloudflare page functions (serverless) environment with our `@pulsebeam/server` package see [demo here](https://github.com/PulseBeamDev/pulsebeam-js/blob/main/demo-react/functions/auth.ts)
1. Use our `rust` src
1. [PulseBeam CLI](/docs/reference/cli/). This can be used for development or for token generation where there is no other SDK available.

**What are we going to review?**

* Implementation Options: Overview for each token-serving method
* Security Best Practices: Recommendations to go with your implementation.

# Implementation Options (Official) 

## 1. JavaScript/TypeScript Runtimes

Use Case: Your existing backend server is in node / deno.

Supported Platforms:
* ✅ Node.js
* ✅ Deno
* ✅ Cloudflare Workers

Steps:
1. Install [SDK](/docs/reference/server-js/)
1. Create an API Endpoint
1. Secure Your Endpoint
1. Serve tokens

### Example `node.js` http server

1. Revisiting our Quickstart

    In our [quickstart project](/docs/getting-started/quick-start/) we generated tokens client-side by embedding our secret key into the browser. For production, we cannot embed our secret key in our clients.

    So if you go back to that project, get your keys (or create new ones). This time were going to create our own auth endpoint.

2. Serve it

    ```bash
    git clone git@github.com:PulseBeamDev/pulsebeam-js.git
    cd pulsebeam-js/demo-cdn
    npm i
    export PULSEBEAM_API_KEY="kid_..."
    export PULSEBEAM_API_SECRET="sk_..."
    npm run start
    ```

    You should now have a server running locally on port 3000

3. See it

    * Go to your browser open two tabs:
        * URL for first tab: <a href="http://localhost:3000/?peerId=peer-29" target="_blank" rel="noreferrer noopener">localhost:3000/?peerId=peer-29</a>
        * URL for second tab: <a href="http://localhost:3000/" target="_blank" rel="noreferrer noopener">localhost:3000/</a>
    * On the second tab enter peer-29 in the first text box. Then click connect.
    * Type text in the bottom text box in either tab. Changes will synchronize between peers in real-time using WebRTC data channels.

    Here's what you should see:
    <iframe style="height: 480px;" id="ytplayer" type="text/html" title="YouTube video player Quickstart Successful Output" width="2000" height="1000"
    src="https://www.youtube.com/embed/Y9mKCrlLu7k?si=HDNMFjK6LaXSy61X?loop=1&modestbranding=1&playsinline=1&color=white&iv_load_policy=3"
    frameborder="0" allowfullscreen allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin"></iframe>

4. Understand it

    The same as our quickstart, we
    1. Started a server
        ```
        HTTP Server (localhost:3000)
                    ├── / -> index.html - frontend code
                    └── /auth - endpoint for token generation
        ```
    2. Experienced a Token Request Workflow

        `index.html` instructs browser to make a request to `/auth` for a token.
        ```
        Browser → GET / -> index.html
        Browser → Request Token from /auth
        ```
        Before the browser was accepting the private key. Which it used to call a remote PulseBeam endpoint instead of embedding the key in your server - local `/auth` endpoint 
    3. Created tokens

        `/auth` uses `@pulsebeam/server` SDK with your environment variables to generate tokens.
        ```
        /auth → @pulsebeam/server SDK → Token → Browser
        ```

**Next steps**: [🔗 See JS Server SDK Documentation](/docs/reference/server-js/)

## 2. Serverless with Cloudflare Page Functions

Use Case: You want to use Cloudflare's environment

Steps:
1. Follow Cloudflare setup to host on Cloudflare page functions. See [Cloudflare docs](https://developers.cloudflare.com/pages/functions/).
1. Create a Cloudflare Function. Checkout our [example page function](https://github.com/PulseBeamDev/pulsebeam-js/blob/main/demo-react/functions/auth.ts).
1. Configure Environment Variables, add PulseBeam Keys.
1. Secure Your Endpoint
1. Serve tokens

## 3. Rust Direct Integration

Use Case: Existing Rust server or bare-metal performance. E.g. Host on bare metal, a cloud provider, a container, etc. Or compile with wasm to another language.

Checkout our [Rust source](https://github.com/PulseBeamDev/pulsebeam-core/).

Steps:
1. Import
1. Create an API Endpoint
1. Secure Your Endpoint
1. Serve tokens

Note: our JS SDK `@pulsebeam/server` is compiled to wasm from this rust src.

## 4. CLI for Non-JS Environments

Use Case: Nothing else works for me

See [PulseBeam CLI Docs](/docs/reference/cli/)

You may want to wrap in a language of your choice.

Python example:
```python
import subprocess

def create_pulsebeam_token(
    api_key: str,
    api_secret: str,
    peer_id: str,
    group_id: str,
    allow_policy: str
) -> subprocess.CompletedProcess:
    command = [
        "./target/debug/pulsebeam-cli",
        "--api-key", api_key,
        "--api-secret", api_secret,
        "create-token",
        "--peer-id", peer_id,
        "--group-id", group_id,
        "--allow-policy", allow_policy
    ]
    
    try:
        result = subprocess.run(
            command,
            check=True,
            text=True,
            capture_output=True
        )
        return result
    except subprocess.CalledProcessError as e:
        print(f"Command failed with error: {e.stderr}")
        raise
```
Example usage:
```python
if __name__ == "__main__":
    try:
        response = create_pulsebeam_token(
            api_key="your_api_key",
            api_secret="your_api_secret",
            peer_id="peer-1",
            group_id="test",
            allow_policy="test:peer-*"
        )
        print("Token created successfully!")
        print(response.stdout)
    except Exception as e:
        print(f"Error creating token: {str(e)}")
```
## Security Best Practices

Checkout our [Security Checklist](/docs/concepts/security-and-architecture#security-checklist)

🔐 Remember: Your security is our priority, and a [shared responsibility](/docs/concepts/security-and-architecture#shared-security-responsibility-model).

# Troubleshooting
| Issue	|Solution |
|---|---|
| Invalid secret format | Check your key format or rotate your key |
| Token Expired |	Implement client-side renewal logic and/or extend TTL|
| Invalid Policy |	Validate policy syntax and string format |
| Unauthorized Peer Connections	| Double-check policy rules, group, and peer IDs|

# Need Help?

* Want an official SDK for your language/platform? Tell us.
* Discuss ideas and designs? Book a call
* Need platform-specific advice?
* Contributions? [repo](https://github.com/PulseBeamDev/pulsebeam-core/tree/main)

**-> [Contact us](/docs/community-and-support/support/)**
