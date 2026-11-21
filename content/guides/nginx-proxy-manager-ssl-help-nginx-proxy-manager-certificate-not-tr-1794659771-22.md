---
title: "Nginx Proxy Manager Certificate Not Trusted: Fix the Upstream Mismatch"
description: "Certificate exists in NPM but browser shows trust error? Learn to check the upstream SSL setting, interpret the result, and verify the fix."
seo_title: "Nginx Proxy Manager Certificate Not Trusted: Fix the Upstream Mismatch"
seo_description: "Certificate exists in NPM but browser shows trust error? Learn to check the upstream SSL setting, interpret the result, and verify the fix."
keywords:
  - "nginx proxy manager certificate not trusted"
date: 2026-11-21T14:49:27+08:00
publishDate: 2026-11-21T14:49:27+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You set up a proxy host in Nginx Proxy Manager, the certificate shows as issued, but your browser still warns that the connection is not trusted.

## Check the upstream SSL setting

Open the proxy host in NPM and go to the SSL tab. Look at the setting for the upstream scheme. If your backend service does not serve HTTPS, the upstream scheme must be http, not https. When NPM forwards to https on a backend that only speaks http, the handshake fails and the browser reports a trust error even though the certificate itself is fine.

## Interpret the result

If the upstream scheme is https, change it to http and save. If it is already http, move to the next check: confirm the certificate is actually selected for this host. In the SSL tab, the certificate dropdown must show the issued certificate, not "None." A certificate that exists in NPM but is not attached to the proxy host will never be served.

## Verification test

After saving, open a private browser window and visit your domain. A valid padlock means the fix worked. If the error persists, run this from a terminal: `openssl s_client -connect yourdomain.com:443 -servername yourdomain.com` and look at the certificate chain. If the issuer is not your expected CA, NPM is still serving a different certificate.

## Rollback note

If changing the upstream scheme breaks the site, revert it immediately and check whether your backend actually requires HTTPS. For persistent cases,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your proxy host settings and the openssl output. This **nginx proxy manager certificate not trusted** issue is almost always a scheme or attachment mismatch, not a broken certificate.
