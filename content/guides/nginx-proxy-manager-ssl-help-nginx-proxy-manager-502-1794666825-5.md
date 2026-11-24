---
title: "Nginx Proxy Manager 502 After Restart: Verify the Upstream Container Network"
description: "Troubleshoot a 502 error after restarting Nginx Proxy Manager when your upstream service is running on its direct port. Check container network connectivity and fix the proxy host."
seo_title: "Nginx Proxy Manager 502 After Restart: Fix Upstream Network"
seo_description: "Troubleshoot a 502 error after restarting Nginx Proxy Manager when your upstream service is running on its direct port. Check container network connectivity and fix the proxy host."
keywords:
  - "nginx proxy manager 502"
date: 2026-11-24T10:01:45+08:00
publishDate: 2026-11-24T10:01:45+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager, and now your proxy host returns 502 even though the upstream service is running fine on its direct port.

## Check the Upstream Container Network

A 502 means NPM cannot reach the upstream. After a restart, container IPs or network attachments can change. First, confirm the upstream container is on the same Docker network as NPM. Run `docker network inspect <npm_network>` and look for both containers. If the upstream is missing, attach it with `docker network connect <npm_network> <upstream_container>`. Next, verify the proxy host's forward hostname. Use the container name (e.g., `myapp`) or its current IP, not `localhost` or `127.0.0.1`. If you see the IP in NPM, compare it to `docker inspect <upstream_container> | grep IPAddress`. A mismatch confirms the issue. This is a common cause of **nginx proxy manager 502** after restarts.

## Verification Test

From inside the NPM container, run `docker exec -it <npm_container> curl -I http://<upstream_host>:<port>`. A `200 OK` or any HTTP response means connectivity is restored. Then reload the proxy host in the browser; the 502 should be gone.

## Rollback and Recovery

If the change breaks other services, revert the forward hostname to its previous value and restart NPM. Keep a note of the working hostname and network for future restarts. For persistent issues,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).

Repeat the same check after a restart and confirm the result twice.

Repeat the same check after a restart and confirm the result twice.

Repeat the same check after a restart and confirm the result twice.
