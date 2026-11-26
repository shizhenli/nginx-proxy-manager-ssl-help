---
title: "Nginx Proxy Manager Redirect Loop HTTPS: Trace the X-Forwarded-Proto Header"
description: "Browser bouncing between HTTP and HTTPS with too many redirects? Learn how to trace the X-Forwarded-Proto header in Nginx Proxy Manager and stop the loop."
seo_title: "Nginx Proxy Manager Redirect Loop HTTPS: Fix the X-Forwarded-Proto Header"
seo_description: "Browser bouncing between HTTP and HTTPS with too many redirects? Learn how to trace the X-Forwarded-Proto header in Nginx Proxy Manager and stop the loop."
keywords:
  - "nginx proxy manager redirect loop https"
date: 2026-11-26T08:00:59+08:00
publishDate: 2026-11-26T08:00:59+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are running a self-hosted service behind Nginx Proxy Manager, and the browser bounces between HTTP and HTTPS until it reports too many redirects.

## Confirm the Loop Source

Open your browser's developer tools, go to the Network tab, and reload the page. Look at the first two requests. If the first is HTTP and returns a 301 or 308 to HTTPS, then the HTTPS request returns another redirect back to HTTP, the loop is confirmed. Note the exact status codes and the Location header values. If both redirects point to the same scheme, the problem is elsewhere, likely a backend misconfiguration.

## Inspect the Forwarded Protocol Header

In Nginx Proxy Manager, open the proxy host for your service and check the Advanced tab. The backend application often decides whether to redirect based on the X-Forwarded-Proto header. If that header is missing or set to http, the backend keeps redirecting to HTTPS even though the client is already there. Add or correct this line in the advanced configuration:

proxy_set_header X-Forwarded-Proto $scheme;

Save the host. This is the most common cause of an **nginx proxy manager redirect loop https** scenario.

## Verify the Fix

Clear your browser cache or use a private window. Request the HTTP URL once and confirm it redirects to HTTPS and stays there. Then request the HTTPS URL directly and confirm it returns a 200 response without a redirect. Check the Network tab again to ensure only one redirect occurs.

## Roll Back if Needed

If the loop persists or the site breaks, remove the added header line and save. The previous configuration returns immediately, so you can test another cause safely.

If the loop continues after this check,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your proxy host settings and the redirect chain you observed.
