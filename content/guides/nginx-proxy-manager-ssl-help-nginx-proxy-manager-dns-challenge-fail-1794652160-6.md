---
title: "Nginx Proxy Manager DNS Challenge Failed: Fix the TXT Record Rejection"
description: "Troubleshoot Nginx Proxy Manager DNS challenge failure when the DNS provider rejects the TXT record. Step-by-step checks, verification, and rollback."
seo_title: "Nginx Proxy Manager DNS Challenge Failed: TXT Record Fix"
seo_description: "Troubleshoot Nginx Proxy Manager DNS challenge failure when the DNS provider rejects the TXT record. Step-by-step checks, verification, and rollback."
keywords:
  - "nginx proxy manager dns challenge failed"
date: 2026-11-15T18:43:25+08:00
publishDate: 2026-11-15T18:43:25+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are issuing a certificate in Nginx Proxy Manager for a self-hosted service, but the process stops at DNS validation because your DNS provider never accepts the TXT record. This is a common **nginx proxy manager dns challenge failed** scenario, and it usually comes down to one of three things: wrong credentials, missing API permissions, or a provider that needs extra configuration.

## Check the DNS Provider Credentials and Permissions

Open the SSL tab for your proxy host and confirm the DNS provider selected matches the one hosting your domain. Then verify the API key or token you entered. Most providers require the key to have permission to create and delete TXT records in the specific zone. If you generated a read-only key, the challenge will fail silently. Re-enter the credentials carefully, checking for trailing spaces.

## Confirm the TXT Record Is Being Created

While the challenge is running, open your DNS provider's control panel and look for a TXT record named something like _acme-challenge.yourdomain.com. If it never appears, the API call is failing. Check Nginx Proxy Manager's logs under the container output for the exact error message from the provider. A 403 or 401 means credentials or permissions; a 404 often means the zone ID or domain name is wrong.

## Verification Test

After correcting credentials, retry the certificate. A successful run will show the TXT record appear briefly, then disappear as validation completes. You can also test manually by creating a TXT record with the value shown in the logs and waiting for DNS propagation using a tool like dig. If the record resolves publicly, the API path is the only remaining issue.

## Rollback and Recovery

If you changed DNS settings or API keys and need to revert, restore your previous credentials and delete any orphaned _acme-challenge TXT records to avoid conflicts. If the certificate still fails,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your logs for a targeted review.
