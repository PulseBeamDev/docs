---
title: Serving Tokens
description: Getting Tokens in Production with Pulsebeam
tableOfContents: { minHeadingLevel: 1, maxHeadingLevel: 4 }
---

**A Production Guide for Developers**

This guide provides production-ready strategies to securely serve PulseBeam tokens to your users. Choose the method that aligns with your infrastructure and platform needs.

:::note
You see [unfamiliar terms?](/docs/concepts/terms)

Want to learn our [security concepts](/docs/concepts/security-and-architecture)?
:::

## Why should I care about **tokens**?

[Tokens](/docs/concepts/terms#token) allow your users to access PulseBeam. They are essential for maintaining a security.

## Why should I care about **Security**

Exposing your secret key (`sk_...`) in client-side code (as we did in our quickstart) compromises all users (+ can affect your usage and bills)

Production environments must serve tokens via secure backend methods.

Key Risks to Avoid:

* 🚫 Client-side secret key exposure
* 🚫 Overprivileged token [policies](/docs/concepts/terms#policy)
* 🚫 Unsecured endpoints

## How to serve tokens?

In our quickstart, we have learned one way to get tokens to your users:
1. ❗ Publish your project's secret key into your client (absolutely insecure, you should not expose your secret key in your clients, this is for development only)

:::caution
**To avoid headaches:** Think about your desired users first. 

Should they be authenticated? (Recommended). If yes, you probably want to leverage existing auth logic and middleware, so co-locate token-generation there.
:::

There are several ways to securely serve tokens:
1. JS Server SDK (`@pulsebeam/server` package) for Node / Deno / Cloudflare environments.
1. Use our `rust` src
1. [PulseBeam CLI](/docs/reference/cli/). This can be used for development or for token generation where there is no other SDK available.

# Implementation Options

## 1. Official SDKs (Recommended)

**Use Case**: Existing JavaScript/TypeScript backend.

`@pulsebeam/server` Example usage
```js
const { API_KEY, API_SECRET } = process.env;
const app = new AccessToken(API_KEY, API_SECRET);

router.post('/auth', (req, res) => {
  const claims = new PeerClaims("myGroup1", "myPeer1");
  const policy = new PeerPolicy("myGroup*", "*");
  claims.setAllowPolicy(policy);

  const ttlSeconds = 3600;
  const token = app.createToken(claims, ttlSeconds);
  res.json({ groupId: "myGroup1", token });
});
```
For more, checkout the `@pulsebeam/server` [SDK documentation](/docs/reference/server-js/)

Supported Platforms:
* ✅ Node.js
* ✅ Deno
* ✅ Cloudflare Page Functions

Hosting Options:
* Node/Deno Servers: Express, Fastify, etc.
* Serverless: AWS Lambda, Cloudflare Page Functions, etc
* Edge Networks: Fly.io, Vercel Edge, etc

Steps:
1. Install [SDK](/docs/reference/server-js/)
1. Create an API Endpoint
1. Secure Your Endpoint
1. Serve tokens

Security Notes:
* 🔒 Always validate user sessions before issuing tokens
* 🔒 Restrict token TTL (e.g., 1 hour instead of 24)
* 🔒 Use environment variables for secrets 

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

### Cloudflare Page Functions

1. Follow Cloudflare setup to host on Cloudflare page functions. See [Cloudflare docs](https://developers.cloudflare.com/pages/functions/).
1. Create a Cloudflare Function. Checkout our [example page function](https://github.com/PulseBeamDev/pulsebeam-js/blob/main/demo-react/functions/auth.ts).
1. Configure Environment Variables, add PulseBeam Keys.
1. Secure Your Endpoint
1. Serve tokens

## 2. Rust Direct Integration

Use Case: Existing Rust server, love 🦀, High-performance, or compile to another [WASM-compatible environment](https://webassembly.org/getting-started/developers-guide/).

Checkout our [Rust source](https://github.com/PulseBeamDev/pulsebeam-core/).

Fun fact, our JS SDK `@pulsebeam/server` is compiled to WASM from this Rust src.

WASM-compatible environments:
* ✅ Go
* ✅ Java
* ✅ PHP
* ✅ C/C++
* ✅ Python
<details><summary>✅ More </summary>
<ul>
<li>✅ AssemblyScript (a TypeScript-like syntax)</li>
<li>✅ C#</li>
<li>✅ Cobol</li>
<li>✅ Dart</li>
<li>✅ F#</li>
<li>✅ Haskell</li>
<li>✅ Kotlin</li>
<li>✅ Moonbit</li>
<li>✅ Swift</li>
<li>✅ Scala.js</li>
<li>✅ D</li>
<li>✅ Pascal</li>
<li>✅ RemObjects Elements</li>
<li>✅ Zig</li>
<li>✅ Grain</li>
<li>✅ Scheme</li>
<li>✅ Ada</li>
<li>✅ Haskell</li>
</ul>
</details>

Example Rust usage
```rs
pub const SANDBOX_API_KEY: &str = "kid_<...>";
pub const SANDBOX_API_SECRET: &str = "sk_<...>";
pub const SANDBOX_DEFAULT_TTL: u32 = 3600;

pub fn router() -> Router {
    let cors = CorsLayer::new()
        .allow_methods([Method::POST])
        .allow_headers([CONTENT_TYPE, ACCEPT_ENCODING, AUTHORIZATION])
        .allow_origin(AllowOrigin::mirror_request())
        .max_age(Duration::from_secs(86400));
    Router::new().route("/token", post(token)).layer(cors)
}

#[derive(Deserialize, Debug, Validate)]
#[serde(rename_all = "camelCase")]
pub struct TokenForm {
    pub api_key: String,
    pub api_secret: String,
    #[validate(regex(path = *RE_ID))] //^[a-zA-Z0-9_-]{1,36}$
    pub group_id: String,
    #[validate(regex(path = *RE_ID))]
    pub peer_id: String,
}

async fn token(
    ValidatedForm(form): ValidatedForm<TokenForm>,
) -> Result<impl IntoResponse, AppError> {
    let claims = pulsebeam_core::PeerClaims {
        group_id: form.group_id,
        peer_id: form.peer_id,
        allow_policy: Some(pulsebeam_core::PeerPolicy {
            group_id: String::from("*"),
            peer_id: String::from("*"),
        }),
    };

    let token = pulsebeam_core::App::new(&form.api_key, &form.api_secret)
        .create_token(&claims, SANDBOX_DEFAULT_TTL)
        .context("failed to create sandbox token")?;
    Ok(token)
}
```

Steps:
1. (Optional) compile to your target language
1. Import
1. Create an API Endpoint
1. Secure Your Endpoint
1. Serve tokens

Security Notes:
* 🔒 Always validate user sessions before issuing tokens
* 🔒 Restrict token TTL (e.g., 1 hour instead of 24)
* 🔒 Use environment variables for secrets 

## 3. CLI for Non-JS Environments

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

Security Alert:
🔐 Avoid storing secrets in scripts. Use temporary credential injection

# Security Best Practices

Mandatory Checks
* Secret Management
    * Never commit `sk_` keys to version control
    * Rotate keys immediately if exposed via [PulseBeam Admin Dashboard](https://cloud.pulsebeam.dev)
* Token Policies
    * Follow least privilege: `PeerPolicy("chat:room-12", "user-*")`
    * Avoid wildcard overuse: `PeerPolicy("*", "*")` when possible
* Network Security
    * Serve tokens over HTTPS only
    * Rate-limit token endpoints (e.g., 10 requests/minute per user)
* Monitoring
    * Alert on unexpected token volume spikes
    * Log token creation metadata (user ID, IP, policy)

Checkout our [Full Security Checklist](/docs/concepts/security-and-architecture#security-checklist)

🔐 Remember: Your security is our priority, and a [shared responsibility](/docs/concepts/security-and-architecture#shared-security-responsibility-model).

# Troubleshooting
| Issue	|Solution |
|---|---|
| Invalid secret format | Check your key format or rotate your key |
| Token Expired |	Implement client-side renewal logic and/or extend TTL|
| Invalid Policy |	Validate policy syntax and string format |
| Unauthorized Peer Connections	| Double-check policy rules, group, and peer IDs check PulseBeam Project usage and limits|

# Need Help?

* Want an official SDK for your language/platform? Tell us.
* Discuss ideas and designs? Book a call
* Need platform-specific advice?
* Contributions? [See Repository](https://github.com/PulseBeamDev/pulsebeam-core/tree/main)

**-> [Contact us](/docs/community-and-support/support/)**
