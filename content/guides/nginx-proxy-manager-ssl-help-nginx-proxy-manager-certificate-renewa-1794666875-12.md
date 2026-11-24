---
title: "Certificate Renewal in NPM: When the Browser Still Doesn't Trust It"
description: "Certificate exists in NPM but browser shows trust error after renewal. Learn how to diagnose the chain and verify trust without breaking your setup."
seo_title: "Nginx Proxy Manager Certificate Renewal: Browser Trust Error Fix"
seo_description: "Certificate exists in NPM but browser shows trust error after renewal. Learn how to diagnose the chain and verify trust without breaking your setup."
keywords:
  - "nginx proxy manager certificate renewal"
date: 2026-11-24T12:02:16+08:00
publishDate: 2026-11-24T12:02:16+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You renewed the certificate in Nginx Proxy Manager, but your browser still shows a trust or handshake error—this guide helps you isolate whether the issue is the served chain or the browser's cache.

## Check the Served Certificate Chain

First, confirm what NPM actually serves. From a terminal, run:

```
openssl s_client -connect yourdomain.com:443 -servername yourdomain.com -showcerts
```

Look at the output. You should see the leaf certificate and any intermediate certificates. If only the leaf appears, the chain is incomplete—browsers will distrust it even though the certificate is valid. In NPM, edit the proxy host, ensure the correct certificate is selected, and save. Then re-run the command. A complete chain shows at least two certificates.

## Verify the Certificate Dates and Subject

In the same output, check the `notBefore` and `notAfter` dates. If the new certificate's start date is in the future (due to clock skew), browsers reject it. Also confirm the subject matches your domain exactly. If NPM issued for `example.com` but you visit `www.example.com`, trust fails. Fix by reissuing with the correct domain or adding a redirect.

## Test in a Clean Browser Context

Open an incognito window or use `curl -vI https://yourdomain.com` and inspect the TLS handshake. If curl succeeds without `--insecure`, the chain is fine and your browser cache is stale. Clear the browser's SSL state or restart it. If curl fails, the problem is server-side.

## Rollback and Recovery

If you changed settings and trust broke, revert to the previous certificate in NPM's SSL tab and save. Then reapply changes one at a time. For persistent issues after **nginx proxy manager certificate renewal**,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your `openssl` output.
