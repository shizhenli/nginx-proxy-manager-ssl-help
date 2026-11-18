---
title: "Certificate Issued but Browser Still Distrusts It: Check the Proxy Host's SSL Tab"
description: "Certificate exists in NPM but the browser still shows a trust or handshake error? Check the Proxy Host's SSL tab, confirm the right certificate is selected, and verify with a direct TLS test."
seo_title: "Nginx Proxy Manager SSL Certificate Not Working: Trust Error Fix"
seo_description: "Certificate exists in NPM but the browser still shows a trust or handshake error? Check the Proxy Host's SSL tab, confirm the right certificate is selected, and verify with a direct TLS test."
keywords:
  - "nginx proxy manager ssl certificate not working"
date: 2026-11-18T08:01:53+08:00
publishDate: 2026-11-18T08:01:53+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You issued a certificate in Nginx Proxy Manager, but the browser still warns that the connection is not trusted or the handshake fails. This usually means the certificate is valid but not actually attached to the proxy host serving your site.

## Check the Proxy Host SSL Tab

Open the proxy host in NPM and go to the SSL tab. Confirm that SSL is enabled, the correct certificate is selected in the dropdown, and Force SSL is on. A certificate can exist under SSL Certificates yet remain unselected here; in that case NPM serves its default self-signed certificate, which browsers reject. If the dropdown shows the wrong domain or an expired entry, switch to the intended certificate and save. This is the most common reason **nginx proxy manager ssl certificate not working** even though issuance succeeded.

## Interpret the Result

After saving, reload the site in a private window. A clean padlock means the proxy host is now serving the correct certificate. If the warning persists, inspect the certificate the browser received: open the site's certificate details and compare the Common Name and issuer with the entry in NPM. A mismatch confirms the proxy host is still bound to another certificate or the change did not apply.

## Verification Test

From a terminal, run openssl s_client -connect yourdomain.com:443 -servername yourdomain.com and read the subject and issuer lines. They should match your NPM certificate, and the verify return code should be 0.

## Rollback Note

If the site breaks after switching certificates, reopen the SSL tab, reselect the previous certificate, and save to restore service before retrying. For hands-on diagnosis,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
