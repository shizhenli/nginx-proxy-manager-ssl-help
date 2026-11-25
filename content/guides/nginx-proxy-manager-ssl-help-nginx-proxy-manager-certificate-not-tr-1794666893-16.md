---
title: "Nginx Proxy Manager Certificate Not Trusted: Diagnose the Upstream Before You Reissue"
description: "Certificate exists in NPM but the browser still shows a trust or handshake error? Follow these checks to find the real cause and restore a trusted connection."
seo_title: "Nginx Proxy Manager Certificate Not Trusted: Fix the Browser Trust Error"
seo_description: "Certificate exists in NPM but the browser still shows a trust or handshake error? Follow these checks to find the real cause and restore a trusted connection."
keywords:
  - "nginx proxy manager certificate not trusted"
date: 2026-11-25T11:01:47+08:00
publishDate: 2026-11-25T11:01:47+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You've issued a certificate in Nginx Proxy Manager, but your browser still warns that the connection isn't trusted—and you're not sure whether the problem is the certificate or something behind it.

## Check the Certificate Chain and Hostname

Open the proxy host in NPM and confirm the certificate is attached to the correct host. Then click the certificate and verify the domain names listed match exactly what you type in the browser, including any subdomain. A certificate for example.com will not satisfy www.example.com. Next, inspect the chain: in your browser, open the certificate details and look at the issuer and the certification path. If the path ends at an unknown or self-signed root, the chain is incomplete. In NPM, reissue with the same DNS or HTTP challenge and ensure the full chain is saved, not just the leaf certificate.

## Confirm the Browser Is Reaching NPM, Not the Upstream

A common cause of an **nginx proxy manager certificate not trusted** error is that the browser is actually hitting the backend service directly, which may present its own self-signed certificate. Check the proxy host's forward hostname and port, and confirm the service is only reachable through NPM. If the upstream also listens on 443, disable its TLS or bind it to localhost so NPM terminates TLS.

## Verification Test

From a terminal, run: openssl s_client -connect yourdomain.com:443 -servername yourdomain.com. Look for 'Verify return code: 0 (ok)'. If you see a non-zero code, the chain or hostname is still wrong.

## Rollback Note

If a change makes things worse, detach the certificate from the proxy host and save; NPM will fall back to its default certificate so you can retry cleanly. For a guided review,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
