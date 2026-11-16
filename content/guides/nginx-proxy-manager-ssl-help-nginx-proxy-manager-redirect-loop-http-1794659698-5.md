---
title: "Nginx Proxy Manager Redirect Loop HTTPS: Isolate the Redirect Source"
description: "Browser stuck in an HTTP/HTTPS redirect loop behind Nginx Proxy Manager? Follow these checks to isolate the redirect source and restore normal access."
seo_title: "Nginx Proxy Manager Redirect Loop HTTPS: Fix Too Many Redirects"
seo_description: "Browser stuck in an HTTP/HTTPS redirect loop behind Nginx Proxy Manager? Follow these checks to isolate the redirect source and restore normal access."
keywords:
  - "nginx proxy manager redirect loop https"
date: 2026-11-16T10:01:42+08:00
publishDate: 2026-11-16T10:01:42+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You set up Nginx Proxy Manager in front of a self-hosted service, enabled Force SSL, and now the browser bounces between HTTP and HTTPS until it reports too many redirects.

## Confirm where the loop starts

Open your browser's developer tools, go to the Network tab, and reload the page with Preserve log enabled. Look at the first two or three requests. If the chain alternates between http:// and https:// for the same host, the loop is real. If it ends on a single https:// request with a 301 back to http://, the upstream service is issuing the redirect, not NPM.

## Check the upstream service's own redirect setting

Many self-hosted apps have a setting like "Force HTTPS" or "Use SSL." If that is enabled while NPM already terminates TLS, the app redirects to HTTPS, NPM forwards to the app over HTTP, and the cycle repeats. Temporarily disable the app's own HTTPS redirect and retry. This is the most common cause of an **nginx proxy manager redirect loop https** symptom.

## Verify the proxy host scheme

In NPM, edit the proxy host and confirm the Scheme is http, not https, unless your upstream truly serves TLS. A mismatch here produces the same bounce.

## Verification test

From a terminal, run `curl -I http://your.domain` and `curl -I https://your.domain`. Each should return a single 301 or 200, not a chain. Then load the site in a private window; it should settle on HTTPS without looping.

## Rollback note

If changes make things worse, re-enable the previous upstream redirect setting and save the proxy host again. Keep a note of the original scheme so you can restore it.

If the loop persists after these checks,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your curl output.
