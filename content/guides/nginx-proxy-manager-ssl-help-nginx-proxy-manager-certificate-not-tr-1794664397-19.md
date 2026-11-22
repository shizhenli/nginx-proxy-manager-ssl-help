---
title: "Nginx Proxy Manager Certificate Not Trusted: The Missing Chain Check"
description: "Certificate issued but browser still distrusts it? Check the intermediate chain in NPM, verify with openssl, and roll back safely."
seo_title: "Nginx Proxy Manager Certificate Not Trusted: Fix the Chain"
seo_description: "Certificate issued but browser still distrusts it? Check the intermediate chain in NPM, verify with openssl, and roll back safely."
keywords:
  - "nginx proxy manager certificate not trusted"
date: 2026-11-22T12:01:59+08:00
publishDate: 2026-11-22T12:01:59+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You set up a self-hosted service behind Nginx Proxy Manager, requested a certificate, and NPM shows it as issued, but the browser still warns that the connection is not trusted.

## Check the certificate chain in NPM
Open the SSL Certificates tab, edit the affected certificate, and look at the certificate details. If the issuer is your CA but the browser reports an incomplete chain, the intermediate certificate is likely missing. NPM needs the full chain, not just the leaf. Paste the intermediate certificate into the Certificate field below the leaf, save, and wait for NPM to reload.

## Interpret the result
If the certificate details show only one certificate and no intermediate, that confirms the chain gap. If the intermediate is present but the browser still fails, compare the certificate's common name and SAN entries with the exact hostname you are visiting. A mismatch in SANs produces a trust error even with a valid chain.

## Verify with a client test
From a machine outside your network, run: openssl s_client -connect your.domain:443 -servername your.domain -showcerts. Look for verify return code 0 (ok). Any other code, especially 21 or 20, points to chain or trust store issues. Also test in a private browser window to rule out cached exceptions.

## Rollback note
If editing the chain breaks HTTPS, revert to the previous certificate version in NPM and restart the container. Keep a copy of the working certificate before changes.

If the chain is complete and the SAN matches, the remaining cause is often a stale browser trust store or a client-side exception. For a controlled diagnosis of this **nginx proxy manager certificate not trusted** state,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
