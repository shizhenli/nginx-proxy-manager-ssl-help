---
title: "Nginx Proxy Manager HSTS Redirect Loop: Isolate the HTTPS Bounce"
description: "Troubleshoot an Nginx Proxy Manager HTTPS redirect loop caused by HSTS and conflicting redirects. Check headers, scheme, and rollback safely."
seo_title: "Nginx Proxy Manager HSTS Redirect Loop: Fix HTTPS Bounce"
seo_description: "Troubleshoot an Nginx Proxy Manager HTTPS redirect loop caused by HSTS and conflicting redirects. Check headers, scheme, and rollback safely."
keywords:
  - "nginx proxy manager hsts redirect"
date: 2026-11-24T09:01:48+08:00
publishDate: 2026-11-24T09:01:48+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are running Nginx Proxy Manager in front of a self-hosted service, and the browser bounces between HTTP and HTTPS until it reports too many redirects.

## Check the redirect chain first

Open your browser developer tools, go to the Network tab, enable Preserve log, and reload the site. Look at the first few requests. If you see a 301 or 308 from HTTP to HTTPS, followed immediately by another redirect back to HTTP, stop there. That back-and-forth is the loop. Note the exact Location header on each response. If the HTTPS response points to an http:// URL, the upstream or a custom location block is forcing the downgrade.

## Inspect HSTS and scheme headers

In Nginx Proxy Manager, open the proxy host and check the Advanced tab for any add_header Strict-Transport-Security line. HSTS itself does not create a loop, but combined with a misconfigured redirect it locks the browser into HTTPS while another rule sends it back. Also confirm the scheme headers: X-Forwarded-Proto should be https when the request arrives over TLS. If your upstream app sees http and redirects to https, while NPM redirects http to https, the two rules can fight. Temporarily disable Force SSL on the proxy host and retest. If the loop stops, the conflict is between NPM's redirect and the upstream's own redirect.

## Verification test

Use curl -I -L --max-redirs 5 https://your.domain and read the status codes. A healthy result ends in 200. A loop shows repeated 301 or 308 responses with alternating Location values. Fix the offending redirect, then repeat until curl settles on 200.

## Rollback and recovery

Before changing headers, export or screenshot the proxy host settings. If the site breaks, re-enable Force SSL and remove any custom HSTS header you added, then save. Clear the browser's HSTS entry for the domain only if you must test over HTTP again. For a controlled diagnosis of your **nginx proxy manager hsts redirect** setup,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
