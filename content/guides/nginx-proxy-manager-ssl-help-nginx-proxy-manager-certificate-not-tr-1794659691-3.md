---
title: "Nginx Proxy Manager Certificate Not Trusted: Check the Proxy Host SSL Binding"
description: "Certificate exists in NPM but the browser still distrusts it? Check the proxy host SSL binding, verify with openssl, and roll back safely."
seo_title: "Nginx Proxy Manager Certificate Not Trusted: Fix SSL Binding"
seo_description: "Certificate exists in NPM but the browser still distrusts it? Check the proxy host SSL binding, verify with openssl, and roll back safely."
keywords:
  - "nginx proxy manager certificate not trusted"
date: 2026-11-16T08:01:43+08:00
publishDate: 2026-11-16T08:01:43+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You have a valid certificate listed in NPM, but when you open your self-hosted service the browser still warns that the connection is not trusted. Before changing DNS or reissuing anything, confirm the certificate is actually attached to the proxy host serving that domain.

## Check the proxy host SSL binding

Open the proxy host for your service and inspect the SSL tab. The certificate dropdown must show the exact certificate you expect, not the default or another domain's cert. Save the host even if nothing appears changed, then reload the browser. If the dropdown was empty or pointed elsewhere, that mismatch is your cause.

## Confirm what the server presents

Run this from a machine outside your LAN:

```
openssl s_client -connect your.domain.com:443 -servername your.domain.com </dev/null 2>/dev/null | openssl x509 -noout -subject -issuer -dates
```

Compare the subject and issuer with the certificate in NPM. If they differ, NPM is serving a different certificate than the one you selected, so revisit the binding step. If they match but the browser still complains, check the system clock on the client and whether the certificate chain is complete.

## Verification test

Load the site in a private window and inspect the certificate viewer. The common name should match your domain and the issuer should be your CA. A clean padlock here confirms the fix.

## Rollback note

If you changed the binding and the site breaks, reopen the proxy host, restore the previous certificate selection, and save. Keep a note of the original setting before editing.

If the **nginx proxy manager certificate not trusted** warning persists after these checks,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your openssl output and a screenshot of the SSL tab.
