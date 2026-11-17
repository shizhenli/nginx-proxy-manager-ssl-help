---
title: "Nginx Proxy Manager HSTS Redirect: Stop the HTTP/HTTPS Loop"
description: "Troubleshoot an nginx proxy manager hsts redirect loop causing too many redirects. Check HSTS headers, force SSL settings, and verify with curl."
seo_title: "Nginx Proxy Manager HSTS Redirect Loop: Fix Too Many Redirects"
seo_description: "Troubleshoot an nginx proxy manager hsts redirect loop causing too many redirects. Check HSTS headers, force SSL settings, and verify with curl."
keywords:
  - "nginx proxy manager hsts redirect"
date: 2026-11-17T10:01:43+08:00
publishDate: 2026-11-17T10:01:43+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are running Nginx Proxy Manager in front of a self-hosted service, and the browser bounces between HTTP and HTTPS until it reports too many redirects.

## Check the HSTS and Force SSL toggles

Open the proxy host in NPM and look at the SSL tab. If Force SSL is enabled and HSTS is also enabled, NPM sends a Strict-Transport-Security header. That header tells the browser to always use HTTPS for this host, even if your upstream or another proxy layer is still redirecting back to HTTP. The loop usually means two layers disagree about which scheme is final.

Disable HSTS first, save, and retest. If the loop stops, the HSTS header was conflicting with an upstream redirect. Re-enable it only after confirming the upstream serves HTTPS directly or trusts the forwarded scheme.

## Confirm what the upstream returns

From a terminal, run:

curl -I http://your-host
curl -I https://your-host

Interpret the results: a 301 or 302 from HTTP to HTTPS is expected. A redirect from HTTPS back to HTTP is the culprit. If the HTTPS response includes a Location header pointing to http://, fix the upstream service or its reverse-proxy config so it does not downgrade.

## Verification test

Use a private window or clear HSTS for the site in your browser, then load the host again. A single redirect to HTTPS with a 200 response confirms the fix. Check the response headers for a single Strict-Transport-Security line, not duplicates.

## Rollback note

If disabling HSTS breaks another host, re-enable it and instead correct the upstream redirect. Keep a note of the original toggles so you can restore them. For a controlled diagnosis of your **nginx proxy manager hsts redirect** setup,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
