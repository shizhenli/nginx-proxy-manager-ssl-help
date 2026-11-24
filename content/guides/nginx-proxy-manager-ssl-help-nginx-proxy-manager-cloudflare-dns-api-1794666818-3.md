---
title: "Cloudflare DNS API Token Rejected During NPM DNS Challenge"
description: "Troubleshoot Nginx Proxy Manager Cloudflare DNS API certificate issuance when DNS validation stalls and the TXT record is never accepted."
seo_title: "Nginx Proxy Manager Cloudflare DNS API: Fix TXT Record Rejection"
seo_description: "Troubleshoot Nginx Proxy Manager Cloudflare DNS API certificate issuance when DNS validation stalls and the TXT record is never accepted."
keywords:
  - "nginx proxy manager cloudflare dns api"
date: 2026-11-24T08:01:49+08:00
publishDate: 2026-11-24T08:01:49+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are issuing a certificate in Nginx Proxy Manager (NPM) for a self-hosted service, but the process stops at DNS validation because Cloudflare never accepts the TXT record.

## Confirm the API Token Permissions

In Cloudflare, go to My Profile > API Tokens and edit the token used in NPM. It must have Zone > DNS > Edit permission for the specific zone. A token with only Zone > Zone > Read will authenticate but cannot create TXT records, so validation fails silently. Also verify the token is not expired or IP-restricted to an address NPM no longer uses.

## Check the Zone and Record Visibility

In NPM, open the SSL tab and confirm the domain exactly matches the Cloudflare zone (for example, app.example.com under example.com). Then, while the challenge is pending, query Cloudflare directly:

dig TXT _acme-challenge.app.example.com @1.1.1.1

If no TXT record appears, the API call is failing. If a record appears but with a different value than the one NPM expects, a stale record from a previous attempt is conflicting. Delete stale _acme-challenge records in the Cloudflare dashboard and retry.

## Verify the API Call

Run a manual test with the same token and zone ID from the NPM host:

curl -X GET "https://api.cloudflare.com/client/v4/zones/ZONE_ID/dns_records?type=TXT" -H "Authorization: Bearer TOKEN"

A 403 or success:false response confirms the token is the problem. A 200 with records means the token works and the issue is in NPM's stored credentials.

## Recovery Note

If you changed the token, re-enter it in NPM and save before retrying. If a certificate request is stuck, delete the pending request in NPM and start a new one; do not keep retrying the same failed job. For persistent **nginx proxy manager cloudflare dns api** errors,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your token scope and dig output.
