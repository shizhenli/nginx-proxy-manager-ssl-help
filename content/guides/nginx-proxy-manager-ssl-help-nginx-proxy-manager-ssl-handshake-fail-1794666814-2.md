---
title: "Nginx Proxy Manager SSL Handshake Failed: Certificate Exists but Browser Distrusts It"
description: "Certificate is issued in NPM but the browser still shows a trust or handshake error. Follow these checks to diagnose and fix the nginx proxy manager ssl handshake failed issue."
seo_title: "Nginx Proxy Manager SSL Handshake Failed: Fix Browser Trust Error"
seo_description: "Certificate is issued in NPM but the browser still shows a trust or handshake error. Follow these checks to diagnose and fix the nginx proxy manager ssl handshake failed issue."
keywords:
  - "nginx proxy manager ssl handshake failed"
date: 2026-11-23T12:01:48+08:00
publishDate: 2026-11-23T12:01:48+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You have a certificate issued in Nginx Proxy Manager, but your browser still shows a trust or handshake error when you visit your self-hosted service.

## Check the certificate chain and hostname

First, confirm the certificate is actually being served for the correct hostname. In NPM, open the proxy host and verify the SSL tab shows the certificate you expect. Then use a terminal to inspect what the server sends:

`openssl s_client -connect yourdomain.com:443 -servername yourdomain.com`

Look for `Verify return code: 0 (ok)`. If it shows a non-zero code like `unable to get local issuer certificate`, the chain is incomplete. Also check that the `subject` and `subjectAltName` match your exact hostname. A mismatch causes a trust error even with a valid certificate.

## Check for mixed content or wrong port

If the certificate is valid and the chain is complete, the browser may still distrust the connection because the page loads resources over HTTP. Open developer tools, go to the Console or Security tab, and look for mixed content warnings. Ensure your service URL uses `https://` and the correct port. If you use a non-standard port, confirm NPM is listening on it and the certificate is applied there.

## Verification test

After any change, run the openssl command again. A successful result shows `Verify return code: 0 (ok)` and the correct hostname. Then reload the browser in a private window to avoid cached errors.

## Rollback note

If a change breaks access, revert to the previous certificate or proxy host settings in NPM and restart the container. Keep a note of the original configuration before editing.

If the **nginx proxy manager ssl handshake failed** error persists after these checks,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your openssl output and NPM logs.
