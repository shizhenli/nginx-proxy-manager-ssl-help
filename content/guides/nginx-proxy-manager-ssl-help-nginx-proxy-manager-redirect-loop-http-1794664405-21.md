---
title: "Nginx Proxy Manager Redirect Loop HTTPS: Isolate the Redirect Before You Change Anything"
description: "Browser bouncing between HTTP and HTTPS in Nginx Proxy Manager? Run one controlled check to isolate the redirect loop before changing settings."
seo_title: "Nginx Proxy Manager Redirect Loop HTTPS: Fix Too Many Redirects"
seo_description: "Browser bouncing between HTTP and HTTPS in Nginx Proxy Manager? Run one controlled check to isolate the redirect loop before changing settings."
keywords:
  - "nginx proxy manager redirect loop https"
date: 2026-11-23T09:01:43+08:00
publishDate: 2026-11-23T09:01:43+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are running Nginx Proxy Manager in front of a self-hosted service, and the browser bounces between HTTP and HTTPS until it reports too many redirects.

## Confirm Where the Loop Starts

Open the proxy host in NPM and note whether Force SSL or HSTS is enabled. Then test with curl from a terminal, replacing the hostname:

`curl -I http://your.host`

Read the first response. A 301 or 302 pointing to `https://your.host` is expected. Follow it once:

`curl -I https://your.host`

If that second response is another redirect back to HTTP, the loop is confirmed. If it returns 200, the loop is coming from the upstream application, not NPM.

## Check the Upstream for Its Own Redirect

Many self-hosted apps redirect to HTTPS themselves. If the app sees plain HTTP from NPM, it may send the browser back to HTTPS, which NPM then redirects again. In NPM, open the proxy host, go to the Custom Locations or Advanced tab, and confirm you are not forwarding the original scheme incorrectly. A common fix is to ensure the upstream receives `X-Forwarded-Proto https` and that the app's own HTTPS redirect is disabled.

## Verification Test

Clear the browser cache or use a private window, then load the site over HTTP once. You should land on HTTPS with a single redirect and a 200 response. Re-run the curl commands above; the second request should no longer return a redirect to HTTP.

## Rollback Note

If changes make things worse, disable Force SSL in the proxy host, save, and retest over HTTP. Restore your previous settings before continuing. This **nginx proxy manager redirect loop https** issue is usually one conflicting redirect, not a broken certificate. If the loop persists after these checks,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your curl output.
