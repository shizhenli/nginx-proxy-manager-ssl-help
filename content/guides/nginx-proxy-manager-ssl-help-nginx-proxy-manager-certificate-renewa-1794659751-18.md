---
title: "Nginx Proxy Manager Certificate Renewal: When the Browser Still Distrusts It"
description: "Certificate exists in NPM but browser shows trust or handshake error. Follow these checks to diagnose the renewal mismatch and restore trust."
seo_title: "Nginx Proxy Manager Certificate Renewal: Browser Still Distrusts It?"
seo_description: "Certificate exists in NPM but browser shows trust or handshake error. Follow these checks to diagnose the renewal mismatch and restore trust."
keywords:
  - "nginx proxy manager certificate renewal"
date: 2026-11-18T11:04:26+08:00
publishDate: 2026-11-18T11:04:26+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You renewed the certificate in Nginx Proxy Manager, but the browser still warns that the connection is not trusted. This usually means the renewed certificate is not the one being served, or the chain is incomplete. Here is how to check without guessing.

## Confirm which certificate NPM is actually serving

Open your proxy host in NPM and verify the SSL tab points to the newly renewed certificate, not an older duplicate. Then, from a terminal, run:

`openssl s_client -connect yourdomain.com:443 -servername yourdomain.com | openssl x509 -noout -dates -issuer`

Compare the notBefore and notAfter dates with the renewal you just issued. If they match the old certificate, NPM is still serving a stale file. If they match the new one, the issue is likely the chain.

## Check the chain and browser trust

In the same output, look for `Verify return code: 0 (ok)`. A non-zero code means the intermediate certificate is missing or mismatched. In NPM, edit the certificate and ensure the full chain is pasted, not just the leaf. Save and reload.

## Verification test

Use a private window or `curl -vI https://yourdomain.com` and confirm the served certificate dates and chain. Then reload the site in the browser. If the warning persists, clear the browser's cached SSL state or test from another device.

## Rollback note

If the new certificate breaks the site, switch the proxy host back to the previous certificate in NPM and save. This restores service while you re-check the renewal.

If these steps do not resolve it,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the openssl output and your NPM certificate details. A controlled **nginx proxy manager certificate renewal** check like this prevents unnecessary reissues.
