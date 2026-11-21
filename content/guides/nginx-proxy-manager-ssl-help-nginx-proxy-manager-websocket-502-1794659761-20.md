---
title: "WebSocket Live Connection Closes Through Nginx Proxy Manager: Diagnose the 502"
description: "Troubleshoot a WebSocket connection that closes through Nginx Proxy Manager with a 502 error. Step-by-step checks for upgrade headers, timeouts, and upstream health."
seo_title: "Nginx Proxy Manager WebSocket 502: Fix Live Connection Drops"
seo_description: "Troubleshoot a WebSocket connection that closes through Nginx Proxy Manager with a 502 error. Step-by-step checks for upgrade headers, timeouts, and upstream health."
keywords:
  - "nginx proxy manager websocket 502"
date: 2026-11-21T14:49:27+08:00
publishDate: 2026-11-21T14:49:27+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

**nginx proxy manager websocket 502**. Your app loads fine, but its live WebSocket connection keeps closing through Nginx Proxy Manager, and you suspect a 502 is involved. Let's walk through the likely causes and how to confirm each one.

## Check the WebSocket upgrade headers
Nginx Proxy Manager forwards WebSocket traffic only if the `Upgrade` and `Connection` headers are passed correctly. In the proxy host's advanced settings, ensure these lines are present:

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

If they are missing, the upstream never sees a WebSocket handshake and may return a 502. Add them, save, and test again.

## Verify the upstream service is listening
A 502 often means Nginx Proxy Manager cannot reach the backend at all. Confirm the service is running and listening on the expected port. From the NPM container, run `curl -i http://<upstream-ip>:<port>`. A successful HTTP response means the backend is reachable; a connection refused or timeout points to a down service or wrong port. Correct the port in the proxy host if needed.

## Inspect timeout settings
WebSocket connections are long-lived. If the proxy_read_timeout is too short, NPM may drop the connection and log a 502. Increase it in advanced settings, for example `proxy_read_timeout 3600s;`, then reload.

## Run a verification test
Open your browser's developer tools, go to the Network tab, and reload the app. Look for the WebSocket request (status 101). If it shows 502, check the NPM error log for upstream errors. A 101 means the handshake succeeded.

## Rollback if needed
If changes make things worse, revert the advanced settings to their previous state and restart NPM. Keep a copy of the original configuration before editing.

Still stuck?

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your NPM logs and upstream details.
