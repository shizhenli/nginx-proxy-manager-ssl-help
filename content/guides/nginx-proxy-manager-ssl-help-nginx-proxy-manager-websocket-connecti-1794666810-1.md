---
title: "WebSocket Connection Fails Through Nginx Proxy Manager: Diagnose the Upgrade"
description: "WebSocket connections closing through Nginx Proxy Manager? Check the proxy host's WebSocket support setting, confirm the upgrade headers, and verify with a browser test."
seo_title: "Nginx Proxy Manager WebSocket Connection Failed: Fix the Upgrade"
seo_description: "WebSocket connections closing through Nginx Proxy Manager? Check the proxy host's WebSocket support setting, confirm the upgrade headers, and verify with a browser test."
keywords:
  - "nginx proxy manager websocket connection failed"
date: 2026-11-23T11:01:53+08:00
publishDate: 2026-11-23T11:01:53+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

Your app loads fine through Nginx Proxy Manager, but its live WebSocket connection closes as soon as it tries to upgrade.

## Check the WebSocket Support Toggle

Open the proxy host in Nginx Proxy Manager and go to the Edit screen. On the Details tab, confirm that WebSockets Support is enabled. When this is off, NPM forwards the HTTP request but does not pass the Upgrade and Connection headers, so the handshake fails and the socket closes. Save the change and wait for the host to redeploy.

## Confirm the Upstream Accepts the Upgrade

If the toggle is already on, the failure is usually upstream. From a shell on the NPM host, test the backend directly with curl -i -H "Upgrade: websocket" -H "Connection: Upgrade" http://backend-ip:port/path. A correct response is 101 Switching Protocols. A 200 or 400 means the service itself is not handling the upgrade; check that it is bound to the right port and that its WebSocket path matches what the client requests. Also confirm no other proxy layer sits between NPM and the service.

## Verify Through the Proxy

Reload the app in a browser, open DevTools, and watch the Network tab for the WebSocket request. A 101 status means the tunnel is restored. A 502 or a closed socket points back to the upstream check above. This also confirms whether the **nginx proxy manager websocket connection failed** at the proxy or at the service.

## Roll Back Safely

If a recent change caused this, revert the proxy host to its previous configuration and save. Keep a note of the working settings before retesting.

If the handshake still fails after these checks,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your proxy host settings and the curl output.
