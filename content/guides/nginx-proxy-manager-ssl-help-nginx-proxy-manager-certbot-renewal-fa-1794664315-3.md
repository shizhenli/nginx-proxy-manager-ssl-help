---
title: "Nginx Proxy Manager Certbot Renewal Failed: Diagnose the ACME Challenge"
description: "A practical troubleshooting guide for when Nginx Proxy Manager certbot renewal fails. Check the ACME challenge path, interpret logs, and verify renewal safely."
seo_title: "Nginx Proxy Manager Certbot Renewal Failed: Fix the ACME Challenge"
seo_description: "A practical troubleshooting guide for when Nginx Proxy Manager certbot renewal fails. Check the ACME challenge path, interpret logs, and verify renewal safely."
keywords:
  - "nginx proxy manager certbot renewal failed"
date: 2026-11-21T14:52:33+08:00
publishDate: 2026-11-21T14:52:33+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You have a working certificate in Nginx Proxy Manager, but when renewal runs, the job fails and the old certificate is not replaced.

## Check the ACME challenge path first

In NPM, open the proxy host and confirm the domain still points to this server. Then check that port 80 is reachable from the public internet. From another machine, run:

`curl -I http://yourdomain.com/.well-known/acme-challenge/test`

If you get a 404, NPM cannot serve the challenge file. If you get a connection refused or timeout, port 80 is blocked or forwarded incorrectly. Either result explains why **nginx proxy manager certbot renewal failed** before any certificate was issued.

## Read the renewal log for the exact error

In NPM, go to the SSL certificates list and open the failing certificate. Check the renewal log for lines containing "Challenge failed" or "Timeout during connect." A timeout means Let's Encrypt could not reach your server on port 80. A 404 means the challenge path is not being served. An "unauthorized" error usually means DNS points elsewhere.

## Verification test

After correcting port forwarding or DNS, trigger a renewal from the certificate menu. Then run:

`echo | openssl s_client -connect yourdomain.com:443 -servername yourdomain.com 2>/dev/null | openssl x509 -noout -dates`

The notAfter date should now be in the future. If it is unchanged, the renewal did not complete.

## Rollback and recovery

If you changed DNS or firewall rules, restore the previous settings before retrying. Do not delete the existing certificate until a new one is issued; NPM keeps the old certificate active if renewal fails. If the log still shows a challenge failure after these checks,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the exact log line.
