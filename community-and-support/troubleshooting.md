---
title: Troubleshooting
description: PulseBeam troubleshooting and debugging reccomendations
tableOfContents: { minHeadingLevel: 1, maxHeadingLevel: 4 }
---

PulseBeam is a flexible peer-to-peer WebRTC Communication Platform (CPaaS) designed to support:
- Peer-to-peer connections
- Global connectivity across different networks and machines
- Seamless communication infrastructure

# Auth Server-Side

Contact [PulseBeam Support](/docs/community-and-support/support/) we can debug and review issues with you. We're always working to improve your developer experience, contacting us can help us improve!

### Preliminary Checks

1. **Environment Variables**
   Verify your API credentials are correctly set:
   ```bash
   echo $PULSEBEAM_API_KEY
   echo $PULSEBEAM_API_SECRET
   ```

2. **Node and npm Version Check**
    
   ```bash
   node -v
   npm -v
   ```
   If versions are outdated, use Node Version Manager (nvm):
   ```bash
   nvm use node
   # Or specify a version e.g.
   nvm use 23.5.0
   ```

## Common Connection Issues

### Port Conflicts
**Symptom**: `Error: listen EADDRINUSE: address already in use :::3000`
**Solution**: 
- Identify and terminate processes using port 3000
- Configure your application to use an alternative port

# Web Client-Side 

## Connection Diagnostics

Contact [PulseBeam Support](/docs/community-and-support/support/) we can debug and review issues with you. We're always working to improve your developer experience, contacting us can help us improve!

0. **Auth Token** - Issues with auth tokens would be apparent before a connection is created
- Verify your client is requesting an auth token
- Verify your token is being received
- Verify the [policy](/docs/concepts/terms#policy) on the token is valid for the connection you are trying to create
- Checkout this for more on [troubleshooting tokens](/docs/getting-started/quick-start#troubleshooting) this for more [understanding tokens](/docs/concepts/terms#token).

1. **Browser WebRTC Internals**
   - Verify SDK version and compatibility
   - Clear browser cache (browser console -> network tab -> clear cache checkbox)
   - Open Chrome -> Your website attempt to connect -> in another tab go to URL `chrome://webrtc-internals/`
   - Inspect connection details, SDP's and ICE Candidates.

2. **Logging and Debugging**
   - Check browser console logs
   - Ensure log levels are set to verbose
   - If no logs are visible, try our [Local Demo app - 5 min Quickstart](/docs/getting-started/quick-start/). 
   - Review PulseBeam admin dashboard for analytics and usage insights

3. **Listen to connection state**
   - Check browser console logs search for `connectionstate`
   - Refer to [Connection concepts](http://localhost:4321/docs/concepts/connection#connection-state) for interpreting connection state meanings

## Handling Connection Failures

**Immediate User Actions**

* Implement connection state listening in your application
* Create a robust UI for users
* Provide clear troubleshooting options:

    - "Try again"
    - "Check your internet connection"
    - "Contact support"

* Add connection restart elements
* Offer actionable feedback about network conditions and internet connectivity


**Recommended Troubleshooting Flow**

* Check network status
* Validate API credentials
* Verify browser compatibility
* Test alternative network connection

# Support and Further Assistance

If issues persist:
- Review logs
- Review PulseBeam docs
- Contact [PulseBeam Support](/docs/community-and-support/support/)

## Best Practices

- Keep SDK and dependencies updated
- Use modern, updated browsers and dependencies
- Implement error handling in your application

## We are missing something? [Contact us](/docs/community-and-support/discord/)

---

**Note**: This guide is a living document. Always refer to the latest version in official documentation.