---
title: "Nginx Proxy Manager Upstream Timeout: Diagnose the Slow Backend"
description: "Troubleshoot Nginx Proxy Manager upstream timeout when the backend is slow but alive. Check proxy timeouts, verify with curl, and restore reliable responses."
seo_title: "Nginx Proxy Manager Upstream Timeout: Diagnose Slow Backend"
seo_description: "Troubleshoot Nginx Proxy Manager upstream timeout when the backend is slow but alive. Check proxy timeouts, verify with curl, and restore reliable responses."
keywords:
  - "nginx proxy manager upstream timeout"
date: 2026-11-25T12:01:48+08:00
publishDate: 2026-11-25T12:01:48+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are running Nginx Proxy Manager in front of a self-hosted service, and the proxy waits then returns a gateway timeout while the upstream is slow but alive. This is a controlled diagnosis for that exact moment before you change anything.

## Confirm the upstream is reachable

From the NPM container, run `curl -v --max-time 30 http://<upstream-ip>:<port>/`. If you see headers but they arrive after several seconds, the service is alive but slow. If the connection is refused, stop here; this is not a timeout issue.

## Check the proxy timeout settings

In NPM, open the proxy host, go to Advanced, and inspect any `proxy_read_timeout`, `proxy_connect_timeout`, or `proxy_send_timeout` directives. Defaults are often 60s. If your upstream legitimately needs longer, raise `proxy_read_timeout` to 120s and save. Do not raise it blindly; first measure the real response time with the curl above.

## Verify the change

Reload the proxy host and repeat the curl through the proxy: `curl -v --max-time 130 https://<your-domain>/`. A 200 response within the new timeout confirms the fix. A 504 again means the upstream is still exceeding the limit or the directive is not applied.

## Rollback if needed

If the timeout change causes other issues, revert the Advanced field to its previous value and save. The old behavior returns immediately.

If the upstream is slow because of its own load, fix that service rather than extending timeouts indefinitely. For a deeper review of your NPM configuration,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).

A measured **nginx proxy manager upstream timeout** adjustment, based on real response times, restores expected behavior without masking a failing backend.
