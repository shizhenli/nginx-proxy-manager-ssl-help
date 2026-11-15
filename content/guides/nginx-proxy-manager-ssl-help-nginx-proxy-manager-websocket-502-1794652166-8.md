---
title: "Nginx Proxy Manager WebSocket 502 After Restart: Verify the Upgrade Headers"
description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port. Check WebSocket upgrade headers and confirm the fix with a simple test."
seo_title: "Fix nginx proxy manager websocket 502 After Restart"
seo_description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port. Check WebSocket upgrade headers and confirm the fix with a simple test."
keywords:
  - "nginx proxy manager websocket 502"
date: 2026-11-15T18:43:25+08:00
publishDate: 2026-11-15T18:43:25+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager, and now the proxy host returns 502 although the upstream service is still running on its direct port.

## Check the WebSocket upgrade headers

Open the proxy host in NPM and go to the Custom Locations or Advanced tab. Confirm these headers are present and spelled exactly:

- `proxy_set_header Upgrade $http_upgrade;`
- `proxy_set_header Connection "upgrade";`

If either line is missing or the Connection value is `close`, NPM drops the upgrade handshake and the upstream closes the connection, producing a 502. Add both lines, save, and restart the proxy host.

## Interpret the result

If the 502 disappears but the WebSocket still fails, the headers are correct and the issue is upstream. If the 502 remains, check the NPM logs for `upstream prematurely closed connection`; that points to a timeout or a mismatched port, not the headers.

## Verification test

From a terminal, run `curl -i -H "Connection: Upgrade" -H "Upgrade: websocket" http://your-proxy-host/`. A successful handshake returns `101 Switching Protocols`. A 502 means the headers did not apply; recheck the exact spelling and save order.

## Rollback note

Before editing, copy the existing Advanced configuration to a text file. If the proxy host fails to start after saving, restore that copy and restart NPM. This keeps the **nginx proxy manager websocket 502** from becoming a full outage.

If the 101 response appears but your app still disconnects, the remaining cause is usually an upstream idle timeout.

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the log lines and the curl output.

Repeat the same check after a restart and confirm the result twice.
