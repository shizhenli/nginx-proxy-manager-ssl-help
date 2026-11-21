---
title: "Nginx Proxy Manager SSL Handshake Failed: When the Certificate Is Issued but the Browser Still Distrusts It"
description: "Troubleshoot an Nginx Proxy Manager SSL handshake failure when the certificate is issued but the browser still distrusts it. Check the served chain, verify with OpenSSL, and recover safely."
seo_title: "Nginx Proxy Manager SSL Handshake Failed: Fix Browser Trust"
seo_description: "Troubleshoot an Nginx Proxy Manager SSL handshake failure when the certificate is issued but the browser still distrusts it. Check the served chain, verify with OpenSSL, and recover safely."
keywords:
  - "nginx proxy manager ssl handshake failed"
date: 2026-11-21T14:52:33+08:00
publishDate: 2026-11-21T14:52:33+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You have a certificate issued in Nginx Proxy Manager, but the browser still shows a trust or handshake error, and you want to restore the expected outcome: certificate is issued but the browser does not trust it.

## Confirm the Browser Is Receiving the Expected Certificate

Open the site in a private window and inspect the certificate details. Check the issuer and the validity dates against the certificate shown in NPM. If they differ, NPM is not serving the certificate you think it is. Then verify from a terminal:

`openssl s_client -connect yourdomain.com:443 -servername yourdomain.com`

Look at the returned chain. A common cause of **nginx proxy manager ssl handshake failed** is a missing intermediate certificate: the leaf is present, but the chain is incomplete. The browser then reports an untrusted or handshake error even though NPM shows the certificate as issued. If the chain is incomplete, reissue or re-upload the full chain in NPM and apply it to the proxy host.

## Check the Proxy Host Binding

In NPM, open the proxy host and confirm the SSL tab has the correct certificate selected and Force SSL is enabled. Save and wait for the reload. A certificate that exists but is not bound to the host will not be served.

## Verification Test

Run the OpenSSL command again. The output should show the full chain ending at a trusted root, and the browser should load without a warning. If the error persists, compare the certificate fingerprint in the browser with the one in NPM.

## Rollback Note

If a change makes things worse, revert to the previous certificate or disable Force SSL temporarily to regain access, then reapply the fix. For guided diagnosis,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
