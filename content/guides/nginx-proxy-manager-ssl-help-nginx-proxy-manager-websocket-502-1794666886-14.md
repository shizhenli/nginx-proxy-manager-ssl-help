---
title: "Nginx Proxy Manager WebSocket 502: Fix the Connection Drop"
description: "Troubleshoot a WebSocket 502 error in Nginx Proxy Manager by checking proxy timeouts and headers, then verify with a simple test."
seo_title: "Nginx Proxy Manager WebSocket 502: Fix the Connection Drop"
seo_description: "Troubleshoot a WebSocket 502 error in Nginx Proxy Manager by checking proxy timeouts and headers, then verify with a simple test."
keywords:
  - "nginx proxy manager websocket 502"
date: 2026-11-25T09:01:45+08:00
publishDate: 2026-11-25T09:01:45+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

Your app loads fine, but its live WebSocket connection keeps closing through Nginx Proxy Manager, and you want to restore a stable connection.

## Check the proxy timeout settings

In Nginx Proxy Manager, open the host's Advanced tab. Add these lines to the custom Nginx configuration:

```
proxy_read_timeout 3600s;
proxy_send_timeout 3600s;
```

Save and wait for the reload. If the WebSocket now stays open, the default 60-second timeout was cutting idle connections. If it still drops immediately, continue below.

## Confirm the upgrade headers

WebSocket needs the Upgrade and Connection headers passed to the backend. In the same Advanced tab, ensure these are present:

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

If the Connection header is set to "close" or missing, the handshake fails and you may see a **nginx proxy manager websocket 502** in the logs. After saving, check the NPM container logs for upstream errors. A 502 here means NPM could not complete the handshake with your service.

## Verify the backend directly

Bypass NPM and connect to your service's WebSocket endpoint from the same network. Use a browser console or a tool like wscat. If the direct connection succeeds but fails through NPM, the issue is in the proxy config. If it fails directly, fix the backend first.

## Rollback and recovery

If changes make things worse, remove the lines you added and save. NPM will reload the previous config. Keep a copy of the original Advanced text before editing.

Still stuck?

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your NPM version and the exact log line.
