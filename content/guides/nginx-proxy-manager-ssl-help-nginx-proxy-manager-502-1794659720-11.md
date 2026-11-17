---
title: "Nginx Proxy Manager 502 After Restart: Verify the Upstream Is Actually Listening"
description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port. Check the upstream listening address and fix the mismatch."
seo_title: "Nginx Proxy Manager 502 After Restart: Check Upstream Listening"
seo_description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port. Check the upstream listening address and fix the mismatch."
keywords:
  - "nginx proxy manager 502"
date: 2026-11-17T11:01:44+08:00
publishDate: 2026-11-17T11:01:44+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted your stack, the upstream service is running on its direct port, but the proxy host now returns 502 before you can run a controlled diagnosis.

## Check the upstream listening address

On the Docker host, run `docker ps` and note the upstream container name. Then run `docker exec <container> netstat -tlnp` (or `ss -tlnp`) inside it. Look at the local address column. If it shows `127.0.0.1:8080`, the service only listens on loopback inside its container. Nginx Proxy Manager connects from another container, so that loopback address is unreachable and you get a **nginx proxy manager 502**.

## Confirm the port and protocol

From the NPM container, run `docker exec <npm-container> curl -v http://<upstream-container>:8080`. A connection refused or timeout confirms the address mismatch. A successful HTTP response means the port is correct and you should check the scheme in the proxy host (HTTP vs HTTPS).

## Apply the fix

Edit the upstream service configuration to listen on `0.0.0.0:8080` instead of `127.0.0.1:8080`, then restart only that container. In NPM, open the proxy host and confirm the forward hostname is the container name and the forward port matches the listening port. Save the host.

## Verification test

Run the same `curl -v` from the NPM container. You should see an HTTP status line. Then load the proxy host URL in a browser; it should return the upstream page, not 502.

## Rollback note

If the service fails to start after changing the bind address, revert to the previous configuration and restart it. If the 502 persists,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the curl output and container names.
