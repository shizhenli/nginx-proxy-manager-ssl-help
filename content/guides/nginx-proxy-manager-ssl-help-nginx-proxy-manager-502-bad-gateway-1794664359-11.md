---
title: "Nginx Proxy Manager 502 After Restart: The Upstream Port Mismatch Check"
description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port. Check for an upstream port mismatch and restore the proxy host."
seo_title: "Nginx Proxy Manager 502 Bad Gateway After Restart: Fix Upstream Port Mismatch"
seo_description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port. Check for an upstream port mismatch and restore the proxy host."
keywords:
  - "nginx proxy manager 502 bad gateway"
date: 2026-11-21T14:55:35+08:00
publishDate: 2026-11-21T14:55:35+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager and now your proxy host returns 502 even though the upstream service still answers on its direct port. This is a common **nginx proxy manager 502 bad gateway** symptom after a restart, and it usually points to the upstream port configured in the proxy host no longer matching the port your service actually listens on.

## Check the upstream port in the proxy host

Open the proxy host in Nginx Proxy Manager and note the Forward Hostname / IP and Forward Port. Then, from the NPM container, test the upstream directly:

```
curl -v http://<upstream-ip>:<forward-port>/
```

Interpret the result precisely:

- If curl connects and returns an HTTP response, the port is correct; the 502 is elsewhere.
- If curl returns connection refused or times out, the forward port is wrong or the upstream is not reachable from the NPM container.

## Compare with the service's direct port

On the upstream host, confirm the listening port:

```
ss -tlnp | grep <service-port>
```

If the service listens on 8080 but the proxy host forwards to 80, that mismatch explains the 502 after restart. Update the Forward Port to the correct value and save.

## Verification test

From the NPM container, rerun the curl command above. A successful HTTP response means the upstream is reachable. Then request the proxy host URL from a browser or curl; it should return the expected page instead of 502.

## Rollback and recovery

If the corrected port still fails, revert the Forward Port to its previous value and restart the NPM container. If the 502 persists,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the curl output and your proxy host settings.
