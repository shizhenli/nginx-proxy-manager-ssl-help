---
title: "WebSocket 502 After Restart: Diagnose the Upgrade Handshake First"
description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port? Check WebSocket upgrade headers and scheme before changing containers."
seo_title: "Nginx Proxy Manager WebSocket 502 After Restart: Fix Upgrade Headers"
seo_description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port? Check WebSocket upgrade headers and scheme before changing containers."
keywords:
  - "nginx proxy manager websocket 502"
date: 2026-11-25T08:01:50+08:00
publishDate: 2026-11-25T08:01:50+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager and now the proxy host returns 502 while the self-hosted service still answers on its direct port. This is a classic **nginx proxy manager websocket 502** symptom: the proxy is rejecting the upgrade handshake, not losing the backend.

## Check the WebSocket headers in the proxy host

Open the proxy host's Advanced tab and confirm these two lines exist exactly:

```
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

If Connection is hardcoded to "upgrade", the handshake can fail after restart. Use the map variable instead. Save and restart the proxy host.

## Verify the upstream scheme and port

In the proxy host's Details tab, confirm the Forward Hostname matches the service's direct address and the Forward Port matches the port you tested. If the service listens on HTTP only, the Scheme must be http, not https. A wrong scheme returns 502 before any WebSocket negotiation.

## Run the verification test

From a terminal, test the handshake directly:

```
curl -i -N -H "Connection: Upgrade" -H "Upgrade: websocket" -H "Sec-WebSocket-Version: 13" -H "Sec-WebSocket-Key: test" http://your-proxy-host/socket
```

A 101 Switching Protocols response means the proxy is forwarding the upgrade. A 502 means the upstream address or scheme is still wrong. Repeat against the direct port; if that also fails, the service is not accepting WebSocket connections.

## Rollback note

If the handshake still fails, revert the Advanced tab to its previous contents and restart the proxy host. Then compare the direct-port test with the proxied test to isolate whether the fault sits in NPM or the service. For a guided review,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
