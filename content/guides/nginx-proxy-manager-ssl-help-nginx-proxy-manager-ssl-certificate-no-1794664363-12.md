---
title: "Certificate Issued but Browser Still Distrusts It: Fix the NPM Trust Chain"
description: "Certificate exists in NPM but browser shows a trust error? Learn how to diagnose the trust chain and fix nginx proxy manager ssl certificate not working."
seo_title: "Nginx Proxy Manager SSL Certificate Not Working: Browser Trust Error Fix"
seo_description: "Certificate exists in NPM but browser shows a trust error? Learn how to diagnose the trust chain and fix nginx proxy manager ssl certificate not working."
keywords:
  - "nginx proxy manager ssl certificate not working"
date: 2026-11-21T14:55:35+08:00
publishDate: 2026-11-21T14:55:35+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You have a certificate issued in Nginx Proxy Manager, but your browser still shows a trust or handshake error for your self-hosted service. This usually means the certificate chain is incomplete or the browser is not receiving the full chain. Here is how to diagnose and fix it.

## Check the certificate chain in NPM

Open NPM, go to SSL Certificates, and edit the certificate for your domain. Look at the certificate details. If the issuer is a well-known CA like Let's Encrypt, the certificate itself is fine. The problem is often the chain. NPM should automatically include the intermediate certificate. Verify that the certificate is not expired and that the domain name matches exactly.

## Test with OpenSSL

Run this command from a machine outside your network:

```
openssl s_client -connect yourdomain.com:443 -servername yourdomain.com
```

Look for the verification result. If it says "verify error: unable to get local issuer certificate," the chain is incomplete. You should see the full chain with the intermediate certificate. If not, reissue the certificate in NPM.

## Check for mixed content or wrong port

Ensure your browser is connecting to the correct port (usually 443) and that no other service is intercepting. Also, confirm that your DNS points to the NPM server, not directly to the backend.

## Verification test

After fixing the chain, clear your browser cache or use a private window. Visit your site. The padlock should appear without warnings. You can also use an SSL checker tool to confirm the chain is complete.

## Rollback note

If reissuing the certificate fails, you can revert to the previous certificate in NPM by restoring a backup or using the previous certificate files. Always keep a backup before making changes.

If the issue persists,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) for a controlled diagnosis. Remember, **nginx proxy manager ssl certificate not working** often comes down to an incomplete chain.
