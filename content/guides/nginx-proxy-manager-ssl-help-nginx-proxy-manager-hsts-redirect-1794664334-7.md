---
title: "Nginx Proxy Manager HSTS Redirect: Stop the HTTP/HTTPS Bounce"
description: "Troubleshoot an nginx proxy manager hsts redirect loop causing too many redirects. Check scheme headers, HSTS settings, and verify with curl before rolling back."
seo_title: "Nginx Proxy Manager HSTS Redirect Loop: Fix Too Many Redirects"
seo_description: "Troubleshoot an nginx proxy manager hsts redirect loop causing too many redirects. Check scheme headers, HSTS settings, and verify with curl before rolling back."
keywords:
  - "nginx proxy manager hsts redirect"
date: 2026-11-21T14:52:33+08:00
publishDate: 2026-11-21T14:52:33+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are running Nginx Proxy Manager in front of a self-hosted service, and at the https redirect loop step the browser bounces between HTTP and HTTPS until it reports too many redirects.

## Check the redirect source first

Open the proxy host in NPM and confirm whether Force SSL or HSTS is enabled. If both are on, and your upstream also redirects HTTP to HTTPS, the loop is guaranteed. Temporarily disable HSTS and Force SSL, save, and retest. If the loop stops, the cause is a duplicate redirect, not a certificate problem.

## Inspect the scheme header

In the Advanced tab, verify you are not forcing `X-Forwarded-Proto` to `http` while NPM terminates TLS. The upstream sees HTTP, redirects to HTTPS, NPM redirects again, and the cycle repeats. The correct value should reflect the original scheme, typically `$scheme`.

## Run a verification test

From a terminal, run `curl -I http://yourdomain.com` and `curl -I https://yourdomain.com`. Each should return a single 301 or 308 to the HTTPS URL, then a 200. More than one redirect hop means the loop persists. Clear HSTS in the browser (chrome://net-internals/#hsts) before retesting, because cached HSTS masks fixes.

## Roll back if needed

If changes make the site unreachable, re-enable the previous settings in NPM and save. Keep a note of the original Force SSL and HSTS state so you can restore it exactly. Do not leave HSTS enabled while diagnosing; it is cached by browsers and hard to undo quickly.

If the loop survives these checks, the interaction between your **nginx proxy manager hsts redirect** settings and the upstream configuration needs a closer look.

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your proxy host and upstream details.
