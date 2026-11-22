---
title: "WebSocket 502 in Nginx Proxy Manager: Diagnose the Upgrade Path"
description: "A practical troubleshooting guide for a WebSocket connection that closes with a 502 error through Nginx Proxy Manager, with checks, result interpretation, a verification test, and rollback advice."
seo_title: "Nginx Proxy Manager WebSocket 502: Fix the Upgrade Path"
seo_description: "A practical troubleshooting guide for a WebSocket connection that closes with a 502 error through Nginx Proxy Manager, with checks, result interpretation, a verification test, and rollback advice."
keywords:
  - "nginx proxy manager websocket 502"
date: 2026-11-22T10:01:49+08:00
publishDate: 2026-11-22T10:01:49+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

## Troubleshooting

Your app loads fine through Nginx Proxy Manager, but its live WebSocket connection keeps closing with a 502 error, and you want to diagnose it without guessing. Start by opening the proxy host in NPM and confirming that the websocket upgrade headers are actually being forwarded. In the advanced tab, the two headers you need are Upgrade and Connection; if they are missing, NPM will not pass the upgrade request to the upstream. The result to interpret: if the app's HTTP pages work but the WebSocket handshake returns 502, the proxy is reaching the upstream for normal requests but failing on the upgrade path, which points to header or timeout handling rather than a dead backend.

Next, check the upstream service's own logs for the handshake attempt. If the upstream logs show no upgrade request at all, the proxy is dropping it before forwarding. If the logs show the request but the service rejects it, the problem is on the service side, not NPM. Also confirm the proxy host's scheme and port match what the service expects for WebSocket traffic; a mismatch here often produces a 502 only on the upgrade request.

For a controlled verification, open your browser's developer tools, load the app, and watch the WebSocket entry in the network panel. A successful fix shows a 101 Switching Protocols response and a stable connection. If it still shows 502, revert any header or timeout changes you made and re-test the plain HTTP path to confirm the app still loads, so you do not leave the proxy in a worse state.

If the handshake still fails after these checks, it is a **nginx proxy manager websocket 502** pattern that needs a closer look at your specific upstream configuration.

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your proxy host settings and upstream logs.
