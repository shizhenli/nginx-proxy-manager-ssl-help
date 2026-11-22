---
title: "Nginx Proxy Manager Certificate Renewal: When the Browser Still Distrusts the New Certificate"
description: "Certificate renewed in NPM but browser still shows a trust error? Check the served certificate chain and fix the mismatch before reissuing."
seo_title: "Nginx Proxy Manager Certificate Renewal: Browser Still Distrusts It?"
seo_description: "Certificate renewed in NPM but browser still shows a trust error? Check the served certificate chain and fix the mismatch before reissuing."
keywords:
  - "nginx proxy manager certificate renewal"
date: 2026-11-22T08:01:49+08:00
publishDate: 2026-11-22T08:01:49+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You renewed the certificate in NPM, it shows as issued, but the browser still warns that the connection is not trusted. That usually means the browser is not receiving the renewed certificate, or it is receiving it with a broken chain. Before you touch DNS or reissue anything, confirm what is actually being served.

## Check the certificate the browser receives

Open the site in a private window and inspect the certificate details. Compare the serial number and expiry date with the certificate shown in NPM. If they differ, NPM is not serving the renewed certificate on that host. In NPM, open the proxy host, confirm the new certificate is selected under SSL, and save. Then reload the browser.

If the serial matches but the browser still distrusts it, check the chain. A missing intermediate certificate causes exactly this symptom. In NPM, the certificate should include the full chain provided by your CA. If you pasted only the leaf certificate, replace it with the fullchain file and save.

## Verification test

From a terminal, run:

```
openssl s_client -connect your.domain:443 -servername your.domain -showcerts
```

Look at the first certificate's subject and dates, then confirm the issuer chain reaches a trusted root. If the chain stops at an intermediate, the browser will not trust it. Also check that the certificate's common name or SAN matches the hostname you are visiting.

## Rollback and recovery

If a change makes things worse, reselect the previous certificate on the proxy host and save; NPM will reload the old configuration. Keep a copy of the working fullchain and key before editing. For persistent chain or renewal mismatches,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the openssl output and the NPM certificate details so the **nginx proxy manager certificate renewal** can be diagnosed without guesswork.
