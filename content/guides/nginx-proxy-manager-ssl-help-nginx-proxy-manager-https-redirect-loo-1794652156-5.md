---
title: "Nginx Proxy Manager HTTPS Redirect Loop: Break the Cycle with One Check"
description: "Stuck in an nginx proxy manager https redirect loop? Learn how to diagnose and fix the HTTP-to-HTTPS bounce with specific checks and a verification test."
seo_title: "Fix Nginx Proxy Manager HTTPS Redirect Loop: Practical Steps"
seo_description: "Stuck in an nginx proxy manager https redirect loop? Learn how to diagnose and fix the HTTP-to-HTTPS bounce with specific checks and a verification test."
keywords:
  - "nginx proxy manager https redirect loop"
date: 2026-11-14T19:23:55+08:00
publishDate: 2026-11-14T19:23:55+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You set up a proxy host in Nginx Proxy Manager, enabled Force SSL, and now your browser bounces between HTTP and HTTPS until it gives up with too many redirects.

## First, confirm where the loop starts

Open your browser's developer tools, go to the Network tab, and reload the page. Look at the first request. If it returns a 301 or 308 to https, then the next HTTPS request returns another redirect back to HTTP, you have an **nginx proxy manager https redirect loop**. The culprit is almost always a mismatch between NPM's Force SSL setting and your upstream service's own redirect rule.

## Check the upstream service

Many self-hosted apps have a setting like "Force HTTPS" or "Use SSL" that redirects HTTP to HTTPS internally. When NPM also forces SSL, the app sees the request as HTTP (because NPM terminates SSL) and redirects again, creating a loop. Log into your service's admin panel and disable its own HTTPS redirect. Save and restart the container if needed.

## Verify the fix

Clear your browser cache or use a private window. Request http://yourdomain.com. You should see a single 301 to https, then a 200 OK. If the loop persists, temporarily disable Force SSL in NPM, save, and test again. If the loop stops, the issue is confirmed as the upstream redirect.

## Rollback if needed

If disabling Force SSL breaks access, re-enable it and instead adjust the upstream to trust the X-Forwarded-Proto header. Most apps have a setting for this. After any change, restart the upstream container and retest.

Still stuck?

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your NPM and upstream logs.
