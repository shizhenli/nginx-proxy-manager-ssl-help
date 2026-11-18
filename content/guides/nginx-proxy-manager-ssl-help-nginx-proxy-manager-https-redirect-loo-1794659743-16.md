---
title: "Nginx Proxy Manager HTTPS Redirect Loop: Isolate the Redirect Source"
description: "Troubleshoot an Nginx Proxy Manager HTTPS redirect loop by isolating whether NPM or the backend forces the redirect, with a verification test and rollback note."
seo_title: "Nginx Proxy Manager HTTPS Redirect Loop: Fix Too Many Redirects"
seo_description: "Troubleshoot an Nginx Proxy Manager HTTPS redirect loop by isolating whether NPM or the backend forces the redirect, with a verification test and rollback note."
keywords:
  - "nginx proxy manager https redirect loop"
date: 2026-11-18T09:01:45+08:00
publishDate: 2026-11-18T09:01:45+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

Your browser bounces between HTTP and HTTPS until it reports too many redirects, and you are stuck at the https redirect loop step before a controlled diagnosis is complete.

## Confirm where the redirect originates

Open the affected proxy host in NPM and check the scheme and port on the Forward Hostname / IP line. If the upstream service expects HTTPS on its own port but NPM forwards to HTTP, the backend may answer with a redirect back to HTTPS. That response passes through NPM and starts the cycle. Also open the Advanced tab and look for a rewrite or return directive that forces HTTPS. If one exists, disable it temporarily and save.

## Test the backend directly

From the NPM container, run curl against the upstream address with the exact scheme and port configured in the proxy host. Use -I to read only headers. Interpret the result precisely: a 301 or 308 pointing to https means the backend is redirecting and NPM is only relaying it. A 200 or 302 without a scheme change means the loop is coming from NPM's own configuration or from a second proxy in front of it.

## Verify the fix

Clear the browser cache or use a private window, then request the host over HTTP once. You should receive a single redirect to HTTPS and a final 200 response. If the browser still cycles, recheck the Advanced tab for a leftover rewrite rule.

## Rollback note

If disabling the advanced directive breaks access, re-enable it and instead correct the upstream scheme or port. Keep a copy of the original Advanced text before editing so you can restore it exactly.

If the loop persists after these checks, it is a **nginx proxy manager https redirect loop** that needs configuration-level review.

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your proxy host settings and the curl headers.
