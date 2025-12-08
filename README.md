[Documentation](https://docs.pulsebeam.dev) | [Issues](https://github.com/PulseBeamDev/docs/issues) | [Discord](https://discord.com/invite/Bhd3t9afuB)


# PulseBeam Docs

<img align="right" src="https://pulsebeam.dev/favicon.svg" height="64px" alt="PulseBeam Logo">

This repository contains the source code and content for the official [PulseBeam Documentation](https://docs.pulsebeam.dev). If you see the current documentation is lacking, please feel free to create pull requests.

## Quickstart

Run the documentation site locally to preview changes.

### Step 1. Install Dependencies

**Node.js 18+ required.**

```bash
npm install
```

### Step 2. Run Development Server

```bash
npm run dev
```

Open http://localhost:3000 to view the docs.

## Writing Documentation

Content is located in `content/docs`. We use **MDX** (Markdown + React components).

### File Structure

*   `content/docs/*.mdx`: The actual documentation pages. The file path determines the URL.
*   `app/source.ts`: Configuration for the content source adapter.

### Adding a Page

Create a new `.mdx` file in `content/docs`. Every file requires a frontmatter block at the top:

```mdx
---
title: Quickstart
description: How to run PulseBeam in 5 minutes.
---

# Quickstart

Your content goes here...
```

### Components

We use Fumadocs, which supports standard Markdown and React components.

**Callouts:**

```tsx
<Callout type="info">
  Host networking is recommended for Linux deployments.
</Callout>
```

**Code Blocks:**

Standard markdown fences (` ``` `) are automatically syntax-highlighted.

## Related

*   **[PulseBeam Server](https://github.com/pulsebeamdev/pulsebeam)**: The core WebRTC SFU.
