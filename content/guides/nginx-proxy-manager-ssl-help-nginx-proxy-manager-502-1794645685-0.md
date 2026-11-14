---
title: "Nginx Proxy Manager 502 After Restart: Check the Upstream Container Network"
description: "Fix a 502 after restarting Nginx Proxy Manager by checking the upstream container network, DNS resolution, and port binding, then verify with curl and roll back if needed."
seo_title: "Nginx Proxy Manager 502 After Restart: Fix Upstream Container Network"
seo_description: "Fix a 502 after restarting Nginx Proxy Manager by checking the upstream container network, DNS resolution, and port binding, then verify with curl and roll back if needed."
keywords:
  - "nginx proxy manager 502"
date: 2026-11-14T19:23:55+08:00
publishDate: 2026-11-14T19:23:55+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager, and now the proxy host returns 502 even though the upstream service still answers on its direct port. This is a common **nginx proxy manager 502** symptom after a restart, and it usually means NPM can no longer reach the upstream by the address you configured, not that the upstream is down.

## Check the upstream address from inside NPM

Open a shell in the Nginx Proxy Manager container and test the exact host and port in your proxy host settings:

```
curl -v http://UPSTREAM_HOST:PORT
```

If this returns connection refused or could not resolve host, the problem is name resolution or network reachability. If it returns an HTTP response, the upstream is reachable and the issue is in the proxy host configuration.

## Check Docker networking

If the upstream runs in Docker, confirm both containers share a network. Run `docker network inspect NETWORK_NAME` and verify both containers appear. If the upstream is on a different network, attach NPM to that network or use the upstream container name instead of localhost. Also confirm the upstream container is still running with `docker ps` and that its port is published or exposed as expected.

## Verification test

After correcting the address or network, reload the proxy host and request the site again. A 200 or the expected application response confirms the fix. If you still see 502, repeat the curl test from inside NPM to isolate whether the failure is DNS, network, or the upstream itself.

## Rollback note

If a recent network or compose change caused this, revert that change and restart both containers. Keep a note of the working upstream address before making further edits. For guided diagnosis,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
