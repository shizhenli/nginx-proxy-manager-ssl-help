---
title: "Nginx Proxy Manager 502 After Restart: Verify the Upstream Port and Container Network"
description: "Nginx Proxy Manager returns 502 after restart even though the upstream service runs on its direct port. Check the upstream port and container network, then verify with curl."
seo_title: "Nginx Proxy Manager 502 After Restart: Fix Upstream Port and Network"
seo_description: "Nginx Proxy Manager returns 502 after restart even though the upstream service runs on its direct port. Check the upstream port and container network, then verify with curl."
keywords:
  - "nginx proxy manager 502 after restart"
date: 2026-11-25T10:01:43+08:00
publishDate: 2026-11-25T10:01:43+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager, and now the proxy host returns 502 even though the upstream service still answers on its direct port.

## Confirm the upstream port and container network

Open the proxy host and check the Forward Hostname / IP and Forward Port. From inside the Nginx Proxy Manager container, test the upstream directly:

```
docker exec -it <npm-container> sh
curl -v http://<upstream-host>:<port>/
```

Interpret the result precisely. A connection refused or timeout means Nginx Proxy Manager cannot reach that host and port, so the 502 is a network or port mismatch, not an application failure. A 200 or 3xx response means the upstream is reachable and the problem is elsewhere in the proxy host configuration.

Next, confirm both containers share a Docker network. Run `docker network inspect <network>` and check that the Nginx Proxy Manager container and the upstream container are both listed. If the upstream was recreated after restart, its IP may have changed; using the container name as the forward hostname on a shared network avoids stale IPs.

## Verification test

After correcting the forward hostname or port, save the proxy host and request the public URL with `curl -I https://<your-domain>/`. A 502 that changes to 200, 301, or 302 confirms the fix. If the 502 persists, re-run the in-container curl to see whether the upstream is still unreachable.

## Rollback and recovery

If your changes make things worse, revert the forward hostname and port to their previous values and restart the Nginx Proxy Manager container. This restores the last known configuration while you diagnose further. For a persistent **nginx proxy manager 502 after restart**,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the curl output and network inspect results.
