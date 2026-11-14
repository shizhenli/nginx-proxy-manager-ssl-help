---
title: "Browser Still Distrusts a Valid NPM Certificate? Diagnose the Upstream First"
description: "Browser shows a trust or handshake error even though the certificate exists in Nginx Proxy Manager? Check the upstream service before you touch the certificate."
seo_title: "Nginx Proxy Manager 502: Check the Upstream Before the Certificate"
seo_description: "Browser shows a trust or handshake error even though the certificate exists in Nginx Proxy Manager? Check the upstream service before you touch the certificate."
keywords:
  - "Nginx Proxy Manager 502: Check the Upstream Before the Certificate"
date: 2026-11-14T19:23:55+08:00
publishDate: 2026-11-14T19:23:55+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You issued a certificate in Nginx Proxy Manager, but the browser still refuses to trust it or throws a handshake error, and you are not sure whether the fault is the certificate or the service behind it.

## Confirm what the browser is actually rejecting

Open the site in a private window and inspect the error text. A certificate warning names the certificate (expired, wrong hostname, untrusted issuer). A handshake or 502-style failure usually means the proxy could not complete a connection to the upstream at all, so the certificate was never the blocker. Write down the exact wording before changing anything.

## Check the upstream before touching the certificate

In Nginx Proxy Manager, open the proxy host and confirm the forward hostname, port, and scheme match what the container actually listens on. From the NPM container, run a quick reachability test against that host and port. If the connection is refused or times out, the upstream is down or on the wrong network, and no certificate change will fix it. This is the moment to apply **Nginx Proxy Manager 502: Check the Upstream Before the Certificate** thinking: prove the backend answers first, then judge the TLS layer. If the upstream responds, move on to certificate checks.

## Verify the certificate path

Use openssl s_client against your domain on port 443 and read the returned chain and expiry date. If the chain is incomplete or the dates are wrong, reissue or reattach the certificate in NPM. If the chain looks correct and the upstream test passed, the browser trust error is likely stale cache or a client-side trust store issue.

## Rollback note

If a change makes things worse, revert the proxy host to its previous forward settings and re-save; the old certificate stays attached and traffic resumes once the upstream is reachable again.

Still stuck after these checks?

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
