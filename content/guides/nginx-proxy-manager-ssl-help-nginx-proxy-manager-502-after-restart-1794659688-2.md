---
title: "Nginx Proxy Manager 502 After Restart: Verify the Upstream Resolves"
description: "Proxy host returns 502 after restart even though your service runs on its direct port. Check upstream hostname resolution inside the NPM container and restore the proxy."
seo_title: "Nginx Proxy Manager 502 After Restart: Fix Upstream Resolution"
seo_description: "Proxy host returns 502 after restart even though your service runs on its direct port. Check upstream hostname resolution inside the NPM container and restore the proxy."
keywords:
  - "nginx proxy manager 502 after restart"
date: 2026-11-15T18:43:25+08:00
publishDate: 2026-11-15T18:43:25+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager, and now the proxy host returns 502 even though your upstream service is still running on its direct port.

## Check upstream resolution inside the NPM container

Open a shell in the Nginx Proxy Manager container and resolve the upstream hostname you entered in the proxy host. Use `getent hosts your-upstream-name` or `ping -c 1 your-upstream-name`. If it returns no address, the proxy cannot reach the backend. That is your fault. If it resolves, note the IP and compare it with the service's actual address.

## Confirm the port and network path

From the same container, test the exact upstream port with `nc -zv your-upstream-name 8080` (replace 8080 with your port). A refused or timed-out connection means the port is wrong or the containers are not on a shared network. A successful connection means the proxy should work; recheck the proxy host's forward hostname and port for typos.

## Verification test

Fix the hostname or port, save the proxy host, and request the site again. A 200 or your service's normal response confirms recovery. If it still fails, restart the NPM container once and retest.

## Rollback and recovery

If a recent edit caused this **nginx proxy manager 502 after restart**, revert the proxy host to its previous forward hostname and port, then save. Keep a note of the working values before changing them again.

If resolution and port checks both pass but the 502 persists,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the exact error from the NPM logs.

Repeat the same check after a restart and confirm the result twice.
