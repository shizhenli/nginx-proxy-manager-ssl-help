---
title: "Nginx Proxy Manager HTTPS Redirect Loop: Stop the Bounce Between HTTP and HTTPS"
description: "Stuck in an HTTPS redirect loop with Nginx Proxy Manager? Learn how to diagnose the bounce between HTTP and HTTPS and restore normal access."
seo_title: "Nginx Proxy Manager HTTPS Redirect Loop: Stop the Bounce"
seo_description: "Stuck in an HTTPS redirect loop with Nginx Proxy Manager? Learn how to diagnose the bounce between HTTP and HTTPS and restore normal access."
keywords:
  - "nginx proxy manager https redirect loop"
date: 2026-11-24T11:01:54+08:00
publishDate: 2026-11-24T11:01:54+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You're running Nginx Proxy Manager in front of a self-hosted service, and your browser keeps bouncing between HTTP and HTTPS until it gives up with a "too many redirects" error.

## Check the Force SSL setting

Open the proxy host in NPM and look at the SSL tab. If "Force SSL" is enabled, NPM adds a redirect from HTTP to HTTPS. That's expected. The loop usually happens when the upstream service also forces HTTPS, or when another proxy in front of NPM already terminates TLS. Check whether your service or an upstream load balancer is issuing its own redirect. If both NPM and the upstream redirect, each request ping-pongs.

## Interpret the result

If the upstream service returns a 301 or 302 to HTTPS, disable its redirect or set it to trust the X-Forwarded-Proto header. If a CDN or external proxy sits before NPM, make sure it forwards the original protocol correctly. When NPM sees HTTP from the proxy, it redirects to HTTPS; the proxy then forwards again as HTTP, and the cycle repeats.

## Verification test

Use curl to see the redirect chain without a browser:

curl -I http://yourdomain.com

Look at the Location header. If it points to https://yourdomain.com and a second request to that HTTPS URL returns another redirect back to HTTP, you've confirmed the loop. Fix the conflicting redirect at the source, then test again.

## Rollback note

If you changed Force SSL or upstream settings and the site breaks, revert the last change in NPM and save. The redirect loop should stop immediately. For persistent cases,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your curl output.

A single conflicting redirect rule is almost always behind this **nginx proxy manager https redirect loop**.
