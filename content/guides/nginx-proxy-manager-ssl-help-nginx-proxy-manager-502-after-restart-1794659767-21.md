---
title: "Nginx Proxy Manager 502 After Restart: Verify the Upstream Health Check"
description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port? Check the upstream health check and restore the proxy."
seo_title: "Nginx Proxy Manager 502 After Restart: Upstream Health Check Fix"
seo_description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port? Check the upstream health check and restore the proxy."
keywords:
  - "nginx proxy manager 502 after restart"
date: 2026-11-21T14:49:27+08:00
publishDate: 2026-11-21T14:49:27+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager, and now the proxy host returns 502 even though the upstream service answers on its direct port.

## Check the upstream health check

Open the proxy host in NPM and confirm the forward hostname and port exactly match what the service listens on. Then, from the NPM container, run `getent hosts <upstream-hostname>` and `curl -v http://<upstream-hostname>:<port>/`. If the hostname resolves but curl fails, the upstream is not reachable from NPM's network; if both succeed, the 502 is likely a stale health check. In NPM, open the proxy host and toggle the health check off, save, then re-enable it and save again. This forces a fresh check after restart.

## Interpret the result

If curl returns a connection refused error, the upstream port is wrong or the service binds only to localhost. If curl times out, a firewall or Docker network is blocking traffic. If curl succeeds but the proxy still returns 502, the health check state is stale and the toggle step above should clear it.

## Verification test

Request the proxy host with `curl -I https://<your-domain>` and confirm a 200 or expected status instead of 502. Also check the NPM logs for upstream connection errors.

## Rollback note

If the toggle does not help, revert the proxy host to its previous forward hostname and port, then restart NPM once more before further changes. This **nginx proxy manager 502 after restart** pattern usually clears once the health check is refreshed. For guided diagnosis,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
