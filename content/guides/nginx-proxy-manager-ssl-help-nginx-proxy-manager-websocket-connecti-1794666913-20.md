---
title: "Nginx Proxy Manager WebSocket Connection Failed: Diagnose the Upgrade Headers"
description: "WebSocket closes through Nginx Proxy Manager while the app loads? Check the proxy host's Upgrade and Connection headers, test with a WebSocket client, and roll back safely."
seo_title: "Nginx Proxy Manager WebSocket Connection Failed: Fix Upgrade Headers"
seo_description: "WebSocket closes through Nginx Proxy Manager while the app loads? Check the proxy host's Upgrade and Connection headers, test with a WebSocket client, and roll back safely."
keywords:
  - "nginx proxy manager websocket connection failed"
date: 2026-11-26T10:00:55+08:00
publishDate: 2026-11-26T10:00:55+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

Your app loads fine through Nginx Proxy Manager, but its live WebSocket connection drops as soon as it tries to upgrade, and you want to fix it before guessing at unrelated settings.

## Check the proxy host's upgrade headers

Open the proxy host in Nginx Proxy Manager and go to the Custom Locations or Advanced tab where you can add custom Nginx configuration. A WebSocket needs two headers forwarded on the upgrade request: Upgrade and Connection. Add them inside the location block that handles the WebSocket path, for example:

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

Save and wait for NPM to reload. If the connection now stays open, the missing headers were the cause. If it still closes, continue below.

## Confirm the backend actually speaks WebSocket

Bypass NPM temporarily by connecting directly to the service's internal IP and port with a WebSocket client such as wscat. If the direct connection also closes, the problem is the backend, not the proxy, and header changes will not help. If direct works but proxied fails, recheck that your custom location matches the exact path the app uses, including any trailing slash.

## Verification test

Reload the app and watch the browser's developer tools Network tab. A successful upgrade shows status 101 Switching Protocols and the connection remains open. A failed one shows a non-101 status or an immediate close after the handshake.

## Rollback note

If the change makes the site unreachable, remove the custom header lines and save; NPM restores the previous configuration on reload. Keep a copy of the original block before editing. If this **nginx proxy manager websocket connection failed** after several attempts,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your proxy host config and the Network tab result.
