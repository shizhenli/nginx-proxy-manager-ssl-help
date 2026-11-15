---
title: "Certificate Renewed but Browser Still Distrusts It? One Check Before You Panic"
description: "Certificate renewed in NPM but browser still shows a trust error? Check the served certificate chain and match it to the proxy host before changing anything."
seo_title: "NPM Certificate Renewal: Browser Still Distrusts It?"
seo_description: "Certificate renewed in NPM but browser still shows a trust error? Check the served certificate chain and match it to the proxy host before changing anything."
keywords:
  - "nginx proxy manager certificate renewal"
date: 2026-11-15T18:43:25+08:00
publishDate: 2026-11-15T18:43:25+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

## Troubleshooting

You renewed a certificate in Nginx Proxy Manager and the browser still refuses to trust it, so you are stuck at the certificate is issued but the browser does not trust it step. Before changing settings, confirm which certificate the browser actually receives. Open the site in a private window, click the padlock or warning icon, and view the certificate. Read the issuer and the validity dates. If the dates are the old ones, the proxy host is still bound to the previous certificate, not the renewed one. Go to Hosts, edit the proxy host, open the SSL tab, and reselect the newly issued certificate from the dropdown. Save and wait a minute. If the dates are current but the issuer is unknown, the chain is incomplete. In NPM, open the certificate and check that the full chain was uploaded, not just the leaf. Re-save it if needed. Then run a verification test from a terminal: openssl s_client -connect your.domain:443 -servername your.domain. Look for verify return code 0 and confirm the presented certificate matches the renewed one. A nonzero code or a mismatched subject means the browser is right to complain. This is also the moment to recheck your **nginx proxy manager certificate renewal** binding, because renewals can succeed while the host keeps the old selection. If you must roll back, switch the proxy host back to the previous certificate and save; HTTPS stays up while you diagnose. For a guided review,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult).
