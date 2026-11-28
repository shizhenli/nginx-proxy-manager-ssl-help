---
title: "Nginx Proxy Manager 502 After Restart: Verify the Upstream Host and Port"
description: "A practical troubleshooting guide for when Nginx Proxy Manager returns 502 after a restart even though the upstream service is running on its direct port. Learn to verify the upstream host and port, run a verification test, and recover safely."
seo_title: "Nginx Proxy Manager 502 After Restart: Fix Upstream Host and Port"
seo_description: "A practical troubleshooting guide for when Nginx Proxy Manager returns 502 after a restart even though the upstream service is running on its direct port. Learn to verify the upstream host and port, run a verification test, and recover safely."
keywords:
  - "nginx proxy manager 502"
date: 2026-11-28T22:11:50+08:00
publishDate: 2026-11-28T22:11:50+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager, and now the proxy host returns 502 even though your self-hosted service is still running on its direct port. This is a common **nginx proxy manager 502** scenario, and it usually points to a mismatch in how the proxy reaches the upstream after restart.

## Check the Upstream Host and Port

Open the proxy host settings and confirm the Forward Hostname / IP and Forward Port. The host must be reachable from inside the Nginx Proxy Manager container, not just from your browser. If the service runs on the same Docker network, use the container name and internal port. If it runs on the host, use the host's LAN IP and published port. A common mistake is using localhost or 127.0.0.1, which points to the NPM container itself, not your service.

## Interpret the Result

If the host is localhost, replace it with the correct container name or host IP. If the port is the published host port but the service listens on a different internal port, correct it. After saving, the proxy host should stop returning 502.

## Verification Test

From the Nginx Proxy Manager container, run a quick connection test to the upstream host and port. If it connects, the proxy path is correct. Then reload the proxy host in your browser; a successful response confirms the fix.

## Rollback and Recovery

If the change makes things worse, revert to the previous host and port, then restart the proxy host. Keep a note of the working values. If the 502 persists after these checks,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your proxy host settings and container network details.
