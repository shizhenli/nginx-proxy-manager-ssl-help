---
title: "Nginx Proxy Manager WebSocket 502: Fix the Live Connection Drop"
description: "Fix a WebSocket 502 through Nginx Proxy Manager by checking the upgrade headers, confirming the backend port, and verifying the handshake."
seo_title: "Nginx Proxy Manager WebSocket 502: Restore Live Connections"
seo_description: "Fix a WebSocket 502 through Nginx Proxy Manager by checking the upgrade headers, confirming the backend port, and verifying the handshake."
keywords:
  - "nginx proxy manager websocket 502"
date: 2026-11-15T18:43:25+08:00
publishDate: 2026-11-15T18:43:25+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You're running Nginx Proxy Manager in front of a self-hosted app, the page loads fine, but its live WebSocket connection keeps closing through the proxy. That points to a failed upgrade handshake rather than a dead backend, so let's isolate it before changing anything else.

## Confirm the failure is the upgrade, not the app

Open your browser's developer tools, go to the Network tab, and reload the page. Filter for the WebSocket request (often a `ws://` or `wss://` entry). If it shows a 502 and the HTTP page itself returns 200, the proxy is rejecting the upgrade. Next, open the proxy host in Nginx Proxy Manager and check the custom locations or advanced configuration for the WebSocket path. The upstream must receive these two headers:

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

If they're missing, the backend never sees an upgrade request and NPM returns a 502. Add them, save, and wait for the config to reload.

## Check the backend port and scheme

In the proxy host's details, confirm the forward hostname and port match what the WebSocket service actually listens on. A common trap is forwarding to an HTTP port when the WebSocket server expects a different one. If the app runs in Docker, verify the container's internal port, not the published one. A mismatch here produces the same **nginx proxy manager websocket 502** symptom even with correct headers.

## Verify the handshake

From a terminal, run:

```
curl -i -N -H "Connection: Upgrade" -H "Upgrade: websocket" -H "Sec-WebSocket-Version: 13" -H "Sec-WebSocket-Key: test" https://your-domain/socket
```

A `101 Switching Protocols` response means the proxy path is fixed. A 502 means the upstream still isn't reachable or the headers aren't applied.

## Roll back if needed

If the change breaks normal HTTP traffic, revert the advanced configuration to its previous state and save; NPM reloads the old config immediately. If you're unsure which block caused it,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) before stacking more edits.
