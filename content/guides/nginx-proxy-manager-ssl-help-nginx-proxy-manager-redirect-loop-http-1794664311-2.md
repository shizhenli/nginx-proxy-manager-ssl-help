---
title: "Nginx Proxy Manager Redirect Loop HTTPS: Stop the Browser Bounce"
description: "Browser stuck in an HTTP to HTTPS redirect loop on Nginx Proxy Manager? Follow these checks to isolate the cause and restore normal HTTPS access."
seo_title: "Nginx Proxy Manager Redirect Loop HTTPS: Fix Too Many Redirects"
seo_description: "Browser stuck in an HTTP to HTTPS redirect loop on Nginx Proxy Manager? Follow these checks to isolate the cause and restore normal HTTPS access."
keywords:
  - "nginx proxy manager redirect loop https"
date: 2026-11-21T14:49:27+08:00
publishDate: 2026-11-21T14:49:27+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You set up Nginx Proxy Manager to force HTTPS, but now the browser bounces between HTTP and HTTPS until it reports too many redirects.

## Check the scheme NPM sees from the upstream

A common cause is a mismatch between the scheme NPM receives and the scheme it forwards. In NPM, open the proxy host and confirm the scheme is set to http for a typical self-hosted service. If the backend itself redirects to HTTPS, NPM's HTTP-to-HTTPS redirect can create a loop. Test by temporarily disabling Force SSL on the proxy host and loading the site over HTTP. If it loads without bouncing, the backend is likely issuing its own redirect.

## Inspect the redirect chain

Use your browser's developer tools, Network tab, and enable Preserve log. Reload the page and look at the first few requests. If you see repeated 301 or 302 responses alternating between http and https, note which server sends each one. A redirect from NPM will usually point to the same host with https. A redirect from the backend may point to a different port or internal hostname. That distinction tells you where to fix the rule.

## Verification test

After adjusting the scheme or disabling Force SSL, clear the browser cache or use a private window. Request the HTTP URL and confirm it reaches the service with a single redirect to HTTPS, then stays there. Check that the final response is 200, not another 301.

## Rollback note

If changes make the site unreachable, re-enable Force SSL and restore the previous scheme setting. Keep a note of the original values before editing.

If the loop persists after these checks, it is a **nginx proxy manager redirect loop https** configuration conflict that needs a closer look at both NPM and the backend.

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the redirect chain details.
