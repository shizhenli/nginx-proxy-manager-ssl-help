---
title: "Nginx Proxy Manager 502 After Restart: Verify the Upstream Port Before Touching the Container"
description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port? Follow these checks to confirm the forwarded port and restore the proxy host."
seo_title: "Nginx Proxy Manager 502 After Restart: Check the Upstream Port"
seo_description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port? Follow these checks to confirm the forwarded port and restore the proxy host."
keywords:
  - "nginx proxy manager 502"
date: 2026-11-21T14:55:35+08:00
publishDate: 2026-11-21T14:55:35+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

Your proxy host started returning 502 right after a restart, yet the upstream service still answers on its direct port, and you want to confirm the forwarded port before changing anything else.

## Check the upstream address and port

Open the proxy host in NPM and look at the Forward Hostname / IP and Forward Port fields. On the machine running the upstream service, run `ss -ltnp` and note the exact address and port the service is bound to. If the service listens on `127.0.0.1:8080` but NPM forwards to port `80`, the proxy cannot reach it and returns 502. The result to interpret is simple: the forwarded port must match a listening port, and the hostname must be reachable from the NPM container, not just from your browser.

## Test from inside the NPM container

Enter the NPM container and run `curl -v http://<upstream-host>:<port>/`. A connection refused or timeout confirms the path is blocked; a 200 or 3xx response means the upstream is reachable and the fault is elsewhere. This is the controlled diagnosis step that separates a port mismatch from a network issue.

## Fix and verify

Correct the Forward Port to the listening port, save the host, and reload it. Then request the proxy host URL again. If it still returns 502, recheck the container network and the upstream bind address. If you changed anything and the result worsens, restore the previous port value and restart NPM to return to the last known state before retrying.

If the **nginx proxy manager 502** persists after these checks,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the curl output and the listening port details.
