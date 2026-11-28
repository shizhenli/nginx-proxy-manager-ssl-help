---
title: "Cloudflare DNS API Challenge Fails in Nginx Proxy Manager: Fix the TXT Record Rejection"
description: "Certificate issuance stops at DNS validation because Cloudflare never accepts the TXT record. Check API token permissions, zone ID, and propagation, then verify with a DNS lookup."
seo_title: "Nginx Proxy Manager Cloudflare DNS API: Fix TXT Record Rejection"
seo_description: "Certificate issuance stops at DNS validation because Cloudflare never accepts the TXT record. Check API token permissions, zone ID, and propagation, then verify with a DNS lookup."
keywords:
  - "nginx proxy manager cloudflare dns api"
date: 2026-11-28T21:43:58+08:00
publishDate: 2026-11-28T21:43:58+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are issuing a certificate in Nginx Proxy Manager with a Cloudflare DNS challenge, and the process stops at DNS validation because Cloudflare never accepts the TXT record.

## Check the API token first

In Cloudflare, open My Profile > API Tokens and confirm the token has Zone:DNS:Edit and Zone:Zone:Read for the specific zone. A Global API Key can work, but scoped tokens are safer. In Nginx Proxy Manager, edit the DNS provider entry and re-enter the token exactly, with no trailing spaces. If you use an email with the Global key, verify it matches the Cloudflare account owner.

## Confirm the zone and record name

Make sure the domain in NPM matches the Cloudflare zone exactly. For example, a certificate for app.example.com should use the example.com zone, and NPM will create _acme-challenge.app.example.com. If you entered the full subdomain as the zone, Cloudflare will reject the TXT record. Check the NPM log for the exact record name it tried to create.

## Test propagation before retrying

After a failed attempt, query the TXT record directly:

dig TXT _acme-challenge.app.example.com +short

If no value appears, Cloudflare did not accept the record. If a value appears but issuance still fails, wait two to five minutes and retry. The **nginx proxy manager cloudflare dns api** path depends on both the token scope and the zone name being correct.

## Rollback and recovery

If you changed the token or zone, revert to the last working DNS provider entry before retrying, then delete any stale _acme-challenge TXT records in Cloudflare. If the challenge still fails,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the exact NPM log line and the record name.
