---
title: "Nginx Proxy Manager 502 After Restart: Verify the Upstream Protocol"
description: "After a restart, your Nginx Proxy Manager proxy host returns 502 even though the upstream service runs on its direct port. Check the upstream scheme setting to resolve this specific 502."
seo_title: "Nginx Proxy Manager 502 Bad Gateway After Restart: Fix Upstream Protocol"
seo_description: "After a restart, your Nginx Proxy Manager proxy host returns 502 even though the upstream service runs on its direct port. Check the upstream scheme setting to resolve this specific 502."
keywords:
  - "nginx proxy manager 502 bad gateway"
date: 2026-11-17T12:01:42+08:00
publishDate: 2026-11-17T12:01:42+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You restarted Nginx Proxy Manager, and now your proxy host returns 502 although the upstream service is running on its direct port.

## Check the Upstream Scheme in the Proxy Host

Open the proxy host in Nginx Proxy Manager and edit it. Look at the scheme field for the upstream. If your service runs plain HTTP on its direct port, the scheme must be http, not https. A restart can revert or misapply this setting if the service was recently changed. Save the proxy host after confirming the scheme matches the service's actual protocol.

## Interpret the Result

If the scheme was wrong, correcting it and saving should immediately clear the 502. If the scheme was already correct, the 502 is likely caused by something else, such as the upstream container not being reachable from the NPM network. In that case, stop here and do not make further changes until you can inspect the upstream logs.

## Verification Test

From a terminal on the NPM host, run a direct request to the upstream: curl -I http://<upstream-ip>:<port>. A successful response (any HTTP status) confirms the upstream is reachable and the scheme is correct. Then reload your proxy host URL in a browser. A normal page load confirms the fix.

## Rollback and Recovery

If the change does not resolve the 502, revert the scheme to its previous value and save. This restores the original state so you can diagnose without compounding the issue. For a guided diagnosis of this **nginx proxy manager 502 bad gateway** scenario,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
