---
title: "Nginx Proxy Manager 502 After Restart: Confirm the Upstream Is Reachable from the Proxy Container"
description: "Fix a 502 after restarting Nginx Proxy Manager by verifying the upstream is reachable from inside the proxy container, with exact checks and a rollback note."
seo_title: "Nginx Proxy Manager 502 Bad Gateway After Restart: Upstream Reachability Check"
seo_description: "Fix a 502 after restarting Nginx Proxy Manager by verifying the upstream is reachable from inside the proxy container, with exact checks and a rollback note."
keywords:
  - "nginx proxy manager 502 bad gateway"
date: 2026-11-28T22:11:50+08:00
publishDate: 2026-11-28T22:11:50+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager and now the proxy host returns 502 even though the upstream service answers on its direct port.

## Check reachability from the proxy container

A 502 means NPM cannot complete a connection to the upstream. The service may be running on the host, but the proxy container may not reach it. Open a shell in the NPM container and test the exact upstream address and port configured in the proxy host:

`curl -v http://<upstream-host>:<port>/`

Interpret the result precisely. If curl returns connection refused, the port is wrong or the service is bound only to localhost. If it times out, a firewall or Docker network is blocking the path. If it returns an HTTP response, the upstream is reachable and the 502 is likely a stale proxy configuration or a DNS name that no longer resolves inside the container.

## Verify the proxy host configuration

In the proxy host, confirm the forward hostname and port match what the container can reach. A common mistake after restart is using `localhost` or `127.0.0.1`, which points to the NPM container itself, not the host. Use the host's LAN IP or the Docker service name. Save the proxy host and test again.

## Verification test

From your workstation, request the proxy host URL and confirm it returns the expected page, not 502. Then repeat the container curl test; both should succeed. If the 502 persists, this is a **nginx proxy manager 502 bad gateway** case that needs the upstream network inspected beyond a single restart.

## Rollback note

If a configuration change caused the failure, revert the forward hostname and port to the last known working values, save, and retest before making further changes. For guided diagnosis,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
