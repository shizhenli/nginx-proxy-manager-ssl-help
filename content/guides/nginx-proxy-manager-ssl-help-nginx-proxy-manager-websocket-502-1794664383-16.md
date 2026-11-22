---
title: "Nginx Proxy Manager WebSocket 502 After Restart: Fix the Upgrade Header"
description: "Proxy host returns 502 after restart even though your upstream service is running. Check the WebSocket upgrade headers in Nginx Proxy Manager and fix the 502."
seo_title: "Nginx Proxy Manager WebSocket 502 After Restart: Fix the Upgrade Header"
seo_description: "Proxy host returns 502 after restart even though your upstream service is running. Check the WebSocket upgrade headers in Nginx Proxy Manager and fix the 502."
keywords:
  - "nginx proxy manager websocket 502"
date: 2026-11-22T09:01:45+08:00
publishDate: 2026-11-22T09:01:45+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

Your Nginx Proxy Manager proxy host returns 502 after a restart even though the upstream service is running fine on its direct port.

## Check the WebSocket upgrade headers

Open the proxy host in Nginx Proxy Manager and go to the Custom Locations or Advanced tab. Look for these two lines in the custom Nginx configuration:

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

If they are missing, Nginx Proxy Manager forwards the request as a normal HTTP request. The upstream WebSocket service rejects it or never completes the handshake, and the proxy host returns 502. Add both lines exactly as shown, save, and restart the proxy host.

If the lines are present, check for a typo: `$http_upgrade` must be lowercase, and `Connection` must be set to `upgrade` in quotes. A missing semicolon or a capitalized variable also causes the same 502.

## Verify the fix

From a terminal, run:

```
curl -i -H "Connection: Upgrade" -H "Upgrade: websocket" https://your-domain.example
```

A working setup returns `101 Switching Protocols`. A 502 means the headers are still not reaching the upstream. If you see 101, open your browser client and confirm the WebSocket connects.

## Rollback note

If the 502 persists after adding the headers, remove your custom lines and save. This restores the previous proxy behavior so you can compare results. Then re-add one line at a time to isolate the cause. For a controlled diagnosis of this **nginx proxy manager websocket 502**,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).

Repeat the same check after a restart and confirm the result twice.
