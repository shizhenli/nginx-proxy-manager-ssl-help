---
title: "Nginx Proxy Manager WebSocket Connection Failed: Diagnose the Upgrade Path"
description: "Troubleshoot a live WebSocket that closes through Nginx Proxy Manager by checking upgrade headers, backend support, and proxy timeouts, then verify with a browser test."
seo_title: "Nginx Proxy Manager WebSocket Connection Failed: Fix the Upgrade Path"
seo_description: "Troubleshoot a live WebSocket that closes through Nginx Proxy Manager by checking upgrade headers, backend support, and proxy timeouts, then verify with a browser test."
keywords:
  - "nginx proxy manager websocket connection failed"
date: 2026-11-21T14:52:33+08:00
publishDate: 2026-11-21T14:52:33+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

## Start with the upgrade handshake

Your app loads fine, but its live WebSocket connection closes through the proxy, so you are stuck before a controlled diagnosis. Treat this as a handshake problem, not a general connectivity problem.

## Check the proxy host's upgrade headers

Open the proxy host in Nginx Proxy Manager and go to the Custom Locations or Advanced tab. Confirm the configuration forwards the upgrade. A working block looks like this:

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
proxy_http_version 1.1;
```

If the Connection header is missing or set to close, the handshake cannot complete. That is the usual cause of an **nginx proxy manager websocket connection failed** result. Save and reload the proxy host after editing.

## Confirm the backend accepts WebSockets

Bypass the proxy temporarily by connecting to the service directly on its container or host port. If the direct connection also closes, the backend is the problem, not NPM. Check that the service is configured to allow WebSocket upgrades and that its port matches the proxy host's forward port.

## Check timeouts

A connection that opens and then drops after a fixed interval usually means a short proxy_read_timeout. Raise it in Advanced to a value longer than your idle interval, then reload.

## Verification test

Open the browser developer tools, go to the Network tab, filter by WS, and reload the app. A successful handshake shows status 101 Switching Protocols and remains open. A 400 or immediate close means the headers are still wrong.

## Rollback note

Keep a copy of the previous Advanced configuration. If the page stops loading after your edit, restore that copy and reload before changing anything else.

If the 101 never appears,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your proxy host settings and the exact close code.
