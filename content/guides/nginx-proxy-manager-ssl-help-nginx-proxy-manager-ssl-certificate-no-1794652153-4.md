---
title: "Nginx Proxy Manager SSL Certificate Not Working: When the Browser Still Distrusts It"
description: "Certificate is issued in NPM but the browser still shows a trust or handshake error. Follow these checks to diagnose and restore trust."
seo_title: "Nginx Proxy Manager SSL Certificate Not Working? Fix Browser Trust"
seo_description: "Certificate is issued in NPM but the browser still shows a trust or handshake error. Follow these checks to diagnose and restore trust."
keywords:
  - "nginx proxy manager ssl certificate not working"
date: 2026-11-14T19:23:55+08:00
publishDate: 2026-11-14T19:23:55+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You have a certificate issued in Nginx Proxy Manager, but the browser still shows a trust or handshake error, and you are stuck before a controlled diagnosis.

## Check the certificate chain and hostname

Open the proxy host in NPM and confirm the certificate is attached to the correct host. Then click the certificate and verify the domain names match exactly what the browser requests. A certificate for example.com will not satisfy www.example.com. Also confirm the certificate is not expired and that the chain is complete. If NPM shows a valid certificate but the browser reports an incomplete chain, the issue is the served chain, not issuance.

## Inspect what the browser actually receives

Use openssl from a client machine: openssl s_client -connect yourdomain.com:443 -servername yourdomain.com. Look for the leaf certificate subject and the chain depth. If the subject does not match your hostname, the wrong certificate is bound. If the chain ends before a trusted root, the intermediate is missing. That is your **nginx proxy manager ssl certificate not working** signal, and it points to the certificate binding or chain, not DNS.

## Verification test

After correcting the binding, reload the proxy host, then rerun the openssl command and confirm the subject matches and the chain includes the intermediate. In the browser, clear the HSTS state or test in a private window. A trusted padlock confirms the fix.

## Rollback note

If a change breaks access, detach the certificate from the proxy host and reattach the previous working one before further edits. If you need a second set of eyes,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
