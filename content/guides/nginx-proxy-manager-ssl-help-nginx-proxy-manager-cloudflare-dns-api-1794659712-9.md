---
title: "Cloudflare DNS API Fails at TXT Validation in Nginx Proxy Manager"
description: "Certificate issuance stalls at DNS validation because Cloudflare rejects the TXT record. Check API token permissions, zone ID, and propagation with these steps."
seo_title: "Nginx Proxy Manager Cloudflare DNS API: Fix TXT Validation Failure"
seo_description: "Certificate issuance stalls at DNS validation because Cloudflare rejects the TXT record. Check API token permissions, zone ID, and propagation with these steps."
keywords:
  - "nginx proxy manager cloudflare dns api"
date: 2026-11-17T09:05:20+08:00
publishDate: 2026-11-17T09:05:20+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You're issuing a certificate in Nginx Proxy Manager and the DNS challenge stops because Cloudflare never accepts the TXT record. This usually means the API token lacks the right scope or the zone lookup is failing.

## Check the API Token Permissions

In Cloudflare, go to My Profile > API Tokens and edit the token used in NPM. It must have Zone > DNS > Edit permission and Zone > Zone > Read permission, scoped to the specific zone. A token with only DNS:Edit often fails because NPM cannot resolve the zone ID. After saving, re-enter the token in NPM's DNS provider settings and retry. If the log still shows an authentication error, regenerate the token and paste it fresh.

## Confirm the Zone ID and Domain Match

The domain you enter in NPM must match the Cloudflare zone exactly, without a subdomain prefix unless the zone itself is that subdomain. If your zone is example.com and you request a certificate for app.example.com, NPM should still find the zone. A mismatch produces a 404 or "zone not found" in the challenge log. Check the NPM container logs for the exact API response.

## Verification Test

Run a manual DNS query after NPM creates the challenge record: dig TXT _acme-challenge.app.example.com @1.1.1.1. If no TXT record appears within two minutes, the API call never succeeded. If it appears but validation still fails, wait for propagation and retry.

## Rollback and Recovery

If you changed token permissions and issuance now fails differently, revert to the previous token and confirm the old error returns. Delete any stale _acme-challenge TXT records in Cloudflare before retrying, then reissue. For persistent API errors with the **nginx proxy manager cloudflare dns api**,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your sanitized logs.
