---
title: "Nginx Proxy Manager Upstream Timeout: A Step-by-Step Diagnosis"
description: "Troubleshoot nginx proxy manager upstream timeout errors when the backend is slow but alive. Follow these checks to isolate the cause and restore service."
seo_title: "Nginx Proxy Manager Upstream Timeout: Diagnose and Fix"
seo_description: "Troubleshoot nginx proxy manager upstream timeout errors when the backend is slow but alive. Follow these checks to isolate the cause and restore service."
keywords:
  - "nginx proxy manager upstream timeout"
date: 2026-11-23T08:01:47+08:00
publishDate: 2026-11-23T08:01:47+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are running Nginx Proxy Manager in front of a self-hosted service, and the proxy waits and returns a gateway timeout while the upstream is slow but alive.

## Confirm the Timeout Source

First, check the NPM logs for the exact error. In NPM, go to Hosts > Proxy Hosts, edit your host, and look at the Advanced tab. Temporarily add `proxy_read_timeout 60s;` and `proxy_connect_timeout 60s;` to see if the timeout shifts. If the error changes to a different timeout value, the upstream is responding but too slowly. If it still times out at the same point, the connection itself may be failing.

## Test the Upstream Directly

From the NPM container, run `curl -v http://<upstream-ip>:<port>/` (replace with your service's address). Interpret the result: if curl hangs and then times out, the upstream is slow to respond. If curl returns quickly, the issue is between NPM and the upstream network path. Also check the upstream service's own logs for slow queries or resource exhaustion.

## Adjust Timeouts and Verify

If the upstream is genuinely slow, increase the timeouts in the Advanced tab of your proxy host. Add `proxy_read_timeout 300s;` and `proxy_send_timeout 300s;`. Save and test again. For a controlled verification, use `curl -I https://your-domain.com` from outside and confirm it returns a 200 or expected status within the new timeout window.

## Rollback and Recovery

If increasing timeouts does not help or causes other issues, remove the custom timeout lines and save. NPM will revert to defaults. Then investigate the upstream service's performance directly. For persistent **nginx proxy manager upstream timeout** cases,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your logs and curl output.
