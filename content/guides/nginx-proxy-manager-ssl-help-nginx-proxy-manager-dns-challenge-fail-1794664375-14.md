---
title: "Nginx Proxy Manager DNS Challenge Failed: When the TXT Record Is Rejected"
description: "Fix Nginx Proxy Manager DNS challenge failure when the DNS provider rejects the TXT record. Step-by-step checks for API credentials, record propagation, and rollback."
seo_title: "Nginx Proxy Manager DNS Challenge Failed: TXT Record Rejected Fix"
seo_description: "Fix Nginx Proxy Manager DNS challenge failure when the DNS provider rejects the TXT record. Step-by-step checks for API credentials, record propagation, and rollback."
keywords:
  - "nginx proxy manager dns challenge failed"
date: 2026-11-21T14:55:35+08:00
publishDate: 2026-11-21T14:55:35+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You're issuing a certificate in Nginx Proxy Manager, but the DNS validation step fails because your DNS provider never accepts the TXT record. This guide helps you pinpoint why the record is rejected and restore successful issuance.

## Check API Credentials and Permissions

First, confirm the DNS provider credentials in NPM are correct and have permission to create TXT records. A read-only API key or expired token will cause silent rejection. In NPM, go to SSL Certificates > Add Certificate > DNS Challenge, select your provider, and re-enter the API key or token. Save and retry. If the log shows an authentication error, the credentials are the problem.

## Verify the TXT Record Is Created

After starting issuance, check your DNS provider's control panel for a new TXT record like `_acme-challenge.yourdomain.com`. If it's missing, NPM never sent the request—likely a credentials or API endpoint issue. If it exists but validation still fails, the record may not have propagated. Use a public DNS checker to query the TXT record; it should return the expected token. If not, wait 5–10 minutes and retry.

## Test Propagation with a Manual Query

Run `dig TXT _acme-challenge.yourdomain.com +short` from a terminal. If you see the token, propagation is fine; if empty, your DNS provider is slow or blocking queries. Some providers require you to disable DNSSEC or use a specific nameserver. Adjust accordingly.

## Rollback and Recovery

If you've made changes to DNS settings, revert them before retrying to avoid conflicts. Delete any stale TXT records manually. If issuance still fails, remove the failed certificate entry in NPM and start fresh. For persistent **nginx proxy manager dns challenge failed** errors, consult your provider's API documentation or

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) for guided troubleshooting.

## Verification Test

After fixing credentials or propagation, retry issuance. A successful DNS challenge will show "Certificate issued successfully" in NPM. Then test HTTPS access to your service; the browser should show a valid certificate.
