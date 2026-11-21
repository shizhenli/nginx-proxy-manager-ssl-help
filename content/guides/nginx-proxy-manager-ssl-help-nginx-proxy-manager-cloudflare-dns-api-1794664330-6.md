---
title: "Cloudflare DNS API: Fixing TXT Record Rejection in Nginx Proxy Manager"
description: "Troubleshoot Cloudflare DNS API certificate issuance in Nginx Proxy Manager when the TXT record is rejected during DNS validation."
seo_title: "Nginx Proxy Manager Cloudflare DNS API: TXT Record Rejection Fix"
seo_description: "Troubleshoot Cloudflare DNS API certificate issuance in Nginx Proxy Manager when the TXT record is rejected during DNS validation."
keywords:
  - "nginx proxy manager cloudflare dns api"
date: 2026-11-21T14:52:33+08:00
publishDate: 2026-11-21T14:52:33+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You're issuing a certificate in Nginx Proxy Manager with Cloudflare as your DNS provider, but the process stops at DNS validation because Cloudflare never accepts the TXT record.

## Check the API Token Permissions

In Cloudflare, go to My Profile > API Tokens. The token used in Nginx Proxy Manager must have Zone:DNS:Edit and Zone:Zone:Read permissions for the specific zone. If the token is scoped to all zones, confirm the zone is included. A token with only Zone:Read will fail silently at TXT creation. Also verify the token hasn't expired.

## Confirm the Zone ID and Domain Match

In Nginx Proxy Manager, edit your DNS challenge entry. The domain field should be the root domain (example.com), not a subdomain, unless you're using a wildcard. If you entered a subdomain, Cloudflare may reject the TXT record because the API call targets the wrong zone. Check that the Zone ID in Cloudflare matches the domain you entered.

## Verify the TXT Record Is Created

Run this from your server: dig TXT _acme-challenge.example.com +short. If you see no TXT record, the API call failed. If you see a TXT record but it doesn't match the value in Nginx Proxy Manager logs, the token lacks edit rights. If you see the correct value but validation still fails, wait 30 seconds and retry—Cloudflare propagation can lag.

## Rollback and Recovery

If you changed the API token or zone settings, revert to the previous token and re-run the certificate request. If the certificate is stuck, delete the failed request in Nginx Proxy Manager and start fresh. For persistent issues with the **nginx proxy manager cloudflare dns api**,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your logs.

## Verification Test

After fixing permissions, request the certificate again. Watch the Nginx Proxy Manager logs for the TXT record value. Then run dig TXT _acme-challenge.example.com +short and confirm it matches. If it does, validation should pass within a minute.
