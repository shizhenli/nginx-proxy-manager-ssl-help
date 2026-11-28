---
title: "Nginx Proxy Manager HSTS Redirect: Break the HTTP-to-HTTPS Loop"
description: "Stuck in an HTTP to HTTPS redirect loop with HSTS in Nginx Proxy Manager? Check the scheme header, interpret results, and verify the fix with one curl test."
seo_title: "Nginx Proxy Manager HSTS Redirect Loop: Fix Too Many Redirects"
seo_description: "Stuck in an HTTP to HTTPS redirect loop with HSTS in Nginx Proxy Manager? Check the scheme header, interpret results, and verify the fix with one curl test."
keywords:
  - "nginx proxy manager hsts redirect"
date: 2026-11-28T21:43:58+08:00
publishDate: 2026-11-28T21:43:58+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

Your browser keeps bouncing between HTTP and HTTPS until it gives up with a too many redirects error, and you need a controlled way to find the cause before changing anything else.

## Check the scheme NPM sees

Open the proxy host in Nginx Proxy Manager and confirm Force SSL is enabled. Then inspect the advanced configuration for a custom redirect that also sends HTTP to HTTPS. Two redirects are fine only if the second one knows the request is already secure. The usual culprit is a missing or wrong scheme header: NPM forwards the request to your backend as HTTP, the backend redirects to HTTPS, and the cycle restarts. In the advanced tab, ensure the standard proxy headers are present, especially X-Forwarded-Proto set to $scheme. If your backend has its own HTTPS redirect, disable one of the two redirects. Interpretation: if removing the backend redirect stops the loop, the backend was misreading the forwarded scheme.

## Verify with one request

From a terminal, run curl -I http://your-domain.example and read the Location header. A single 301 or 308 to the HTTPS URL is correct. If you see the same HTTP URL returned repeatedly, a redirect rule is matching its own output. Then run curl -I https://your-domain.example and confirm a 200 or your expected backend status, not another redirect.

## Rollback and recovery

Before editing, copy the advanced configuration to a text file. If the site breaks, paste the original back and save; NPM reloads quickly. Clear the browser's HSTS entry only if you must test HTTP again, since HSTS forces HTTPS at the browser level.

If the loop persists after these checks, the interaction between your **nginx proxy manager hsts redirect** settings and the backend needs a closer look.

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your advanced config and curl output.
