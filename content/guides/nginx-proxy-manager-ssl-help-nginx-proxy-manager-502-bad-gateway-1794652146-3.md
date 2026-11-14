---
title: "Nginx Proxy Manager 502 After Restart: Fix the Upstream Port Mismatch"
description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port. Check the upstream port and scheme in NPM, then verify with curl."
seo_title: "Fix nginx proxy manager 502 bad gateway after restart"
seo_description: "Proxy host returns 502 after restart even though the upstream service runs on its direct port. Check the upstream port and scheme in NPM, then verify with curl."
keywords:
  - "nginx proxy manager 502 bad gateway"
date: 2026-11-14T19:23:55+08:00
publishDate: 2026-11-14T19:23:55+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager and now your proxy host returns 502, even though the upstream service still answers on its direct port. This points to a mismatch between what NPM forwards to and where the service actually listens. Work through these checks in order and stop at the first one that fails.

## Check the upstream port and scheme

Open the proxy host in NPM and look at the Forward Hostname / IP and Forward Port fields. Confirm the port matches the one that works when you browse the service directly. If the service listens on 8080 but NPM forwards to 80, you get a 502. Also confirm the scheme: an HTTPS upstream on a plain HTTP port, or the reverse, will fail the same way. Save the host after any change.

## Confirm the container is reachable from NPM

If NPM runs in Docker, the forward hostname must be resolvable from inside the NPM container, not just from your desktop. Use the container name or the Docker network IP, not localhost. A hostname that only resolves on the host machine produces a 502 every time.

## Verification test

From the NPM container, run a request to the upstream: curl -I http://upstream-host:port. A 200 or 3xx response means NPM can reach the service and the proxy host should recover. A connection refused or timeout means the port or hostname is still wrong.

## Rollback and recovery

If a recent edit caused this, revert the proxy host to its last working forward settings and save. If you cannot recall them, recreate the host with the direct port that works. For a guided diagnosis of your **nginx proxy manager 502 bad gateway** case,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
