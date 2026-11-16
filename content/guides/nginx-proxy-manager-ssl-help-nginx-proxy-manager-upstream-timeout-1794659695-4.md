---
title: "Nginx Proxy Manager Upstream Timeout: Diagnose a Slow but Alive Backend"
description: "Troubleshoot nginx proxy manager upstream timeout when your backend is slow but alive. Check proxy timeouts, verify with curl, and roll back safely."
seo_title: "Nginx Proxy Manager Upstream Timeout: Fix a Slow Backend"
seo_description: "Troubleshoot nginx proxy manager upstream timeout when your backend is slow but alive. Check proxy timeouts, verify with curl, and roll back safely."
keywords:
  - "nginx proxy manager upstream timeout"
date: 2026-11-16T09:01:41+08:00
publishDate: 2026-11-16T09:01:41+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are running Nginx Proxy Manager in front of a self-hosted service, and requests hang until the proxy returns a gateway timeout even though the backend is still running. This is a classic **nginx proxy manager upstream timeout**, and it usually means the proxy gave up before your slow service could answer.

## Confirm the Upstream Is Slow, Not Dead

First, test the backend directly from the NPM host, bypassing the proxy. Run a curl with a generous timeout:

```
curl -v --max-time 60 http://<upstream-ip>:<port>/
```

If curl eventually returns a response, the upstream is alive but slow. If it times out too, the problem is inside the service, not NPM. Also check the NPM container logs for the exact error: `upstream timed out (110: Connection timed out) while reading response header from upstream` confirms the proxy deadline was hit.

## Inspect the Proxy Timeout Settings

In NPM, open the proxy host and look at the advanced configuration. The default `proxy_read_timeout` is 60 seconds. If your service needs longer, add:

```
proxy_read_timeout 300s;
proxy_connect_timeout 60s;
proxy_send_timeout 300s;
```

Save and let NPM reload. Interpretation: if the timeout error disappears but responses are still slow, you have fixed the symptom and can now profile the backend.

## Verify the Fix

Repeat the same request through the proxy hostname with `curl -v --max-time 300 https://your.domain/`. A successful response with a 200 status and no timeout line in the NPM logs means the proxy now waits long enough. Test twice to rule out caching.

## Roll Back if Needed

If the longer timeout causes hung connections or memory pressure, remove the custom lines and save. NPM reverts to defaults immediately. For persistent tuning,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) before changing more directives.
