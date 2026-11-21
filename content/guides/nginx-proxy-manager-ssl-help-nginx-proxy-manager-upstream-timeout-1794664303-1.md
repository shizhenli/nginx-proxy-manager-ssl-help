---
title: "Nginx Proxy Manager Upstream Timeout: When the Backend Is Slow but Alive"
description: "Diagnose an nginx proxy manager upstream timeout when the backend is slow but alive. Check proxy timeouts, verify with curl, and roll back safely."
seo_title: "Nginx Proxy Manager Upstream Timeout: Slow Backend Fix"
seo_description: "Diagnose an nginx proxy manager upstream timeout when the backend is slow but alive. Check proxy timeouts, verify with curl, and roll back safely."
keywords:
  - "nginx proxy manager upstream timeout"
date: 2026-11-21T14:49:27+08:00
publishDate: 2026-11-21T14:49:27+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are running Nginx Proxy Manager in front of a self-hosted service, and the proxy waits then returns a gateway timeout while the upstream is slow but alive.

## Confirm the upstream is actually slow

From the NPM host, run `curl -v --max-time 60 http://<upstream-ip>:<port>/` and watch the timing. If the connection opens but the response body takes longer than NPM's default 60s proxy_read_timeout, you have found the trigger. A refused connection or instant 502 points elsewhere; a slow but complete response confirms the timeout path.

## Check the proxy timeout values

Open the proxy host in NPM, go to Advanced, and inspect any custom `proxy_read_timeout`, `proxy_connect_timeout`, and `proxy_send_timeout` directives. If none are set, NPM falls back to 60s. Compare that number with your curl timing. If the upstream needs 90s, set `proxy_read_timeout 120s;` and `proxy_send_timeout 120s;` in the Advanced box, then save. This is the usual fix for an **nginx proxy manager upstream timeout** where the backend is merely slow.

## Verify the change

Reload the proxy host and repeat the curl test through the public hostname: `curl -v --max-time 130 https://<your-domain>/`. A 200 response within the new window confirms the timeout was the cause. If it still times out, the upstream itself is stalled, not the proxy.

## Roll back if needed

If raising timeouts does not help, remove the custom directives to restore defaults, then investigate upstream health and resource limits. Keep a note of the original values so you can revert cleanly.

If the timing still does not add up,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your curl output and Advanced config.
