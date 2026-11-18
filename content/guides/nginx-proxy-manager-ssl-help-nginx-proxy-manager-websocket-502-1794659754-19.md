---
title: "Nginx Proxy Manager WebSocket 502 After Restart: Check the Upgrade Header"
description: "Fix a 502 after restart when your upstream service runs fine on its direct port. Check the WebSocket Upgrade header in Nginx Proxy Manager and verify with a controlled test."
seo_title: "Nginx Proxy Manager WebSocket 502 After Restart: Fix the Upgrade Header"
seo_description: "Fix a 502 after restart when your upstream service runs fine on its direct port. Check the WebSocket Upgrade header in Nginx Proxy Manager and verify with a controlled test."
keywords:
  - "nginx proxy manager websocket 502"
date: 2026-11-18T12:02:30+08:00
publishDate: 2026-11-18T12:02:30+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager, and now the proxy host returns 502 even though your self-hosted service answers on its direct port.

## Confirm the upstream is reachable

From the NPM container, run `curl -v http://<upstream-ip>:<port>/` using the exact host and port in your proxy host. A `200` or `101` means the upstream is fine and the fault is in the proxy configuration. A connection refused or timeout means fix networking first.

## Check the WebSocket Upgrade headers

Open the proxy host, go to Advanced, and inspect the custom Nginx configuration. For WebSocket routes, the proxy must forward the upgrade handshake. A common cause of the **nginx proxy manager websocket 502** is a missing or duplicated `proxy_set_header Upgrade $http_upgrade;` and `proxy_set_header Connection "upgrade";` pair. If the block is absent, add it. If it appears twice, remove the duplicate. Save and restart the proxy host.

## Verify with a controlled test

Use `curl -i -H "Connection: Upgrade" -H "Upgrade: websocket" http://<your-domain>/` and look for `101 Switching Protocols`. A `502` here confirms the proxy still cannot complete the upstream handshake. A `101` means the route is restored.

## Rollback if needed

If the change makes things worse, revert the Advanced block to its previous content and restart. Keep a copy of the original configuration before editing.

If the 101 test still fails,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your curl output and proxy host settings.

Repeat the same check after a restart and confirm the result twice.

Repeat the same check after a restart and confirm the result twice.
