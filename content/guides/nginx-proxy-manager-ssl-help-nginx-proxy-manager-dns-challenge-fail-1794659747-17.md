---
title: "Nginx Proxy Manager DNS Challenge Failed: When the Provider Rejects the TXT Record"
description: "Certificate issuance stops at DNS validation because your DNS provider never accepts the TXT record. Check API credentials, zone scope, and propagation with a direct query, then roll back safely."
seo_title: "Fix Nginx Proxy Manager DNS Challenge Failed at Validation"
seo_description: "Certificate issuance stops at DNS validation because your DNS provider never accepts the TXT record. Check API credentials, zone scope, and propagation with a direct query, then roll back safely."
keywords:
  - "nginx proxy manager dns challenge failed"
date: 2026-11-18T10:09:03+08:00
publishDate: 2026-11-18T10:09:03+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

You are issuing a certificate in Nginx Proxy Manager and the process halts at DNS validation because your provider never accepts the TXT record. That usually means the plugin cannot create the record, not that Let's Encrypt is slow.

## Confirm the plugin is actually writing the record

Open the certificate request log and look for the exact API response from your DNS provider. A 401 or 403 means the API token is wrong or lacks write permission for the zone. A 404 or "zone not found" means the zone name in the credentials does not match the registered domain. A 400 with "record already exists" means a stale TXT record from a previous attempt is blocking the new one. Each message points to one fix: regenerate the token with edit access, correct the zone, or delete the leftover record.

## Check propagation before blaming NPM

Query the TXT record directly from a public resolver while the request is paused or immediately after it fails:

dig TXT _acme-challenge.yourdomain.com @1.1.1.1 +short

If the answer is empty, the provider never accepted the write. If it returns a value different from the one in the NPM log, you have a stale or duplicate record. If it matches, the challenge should pass on retry.

## Recovery and retry

If you changed credentials or deleted records, remove the failed certificate entry in NPM before retrying so the old challenge state is cleared. Keep the previous working certificate in place until the new one issues; do not delete it first. If the log still shows a provider rejection after these checks, this **nginx proxy manager dns challenge failed** state is usually a token scope or zone mismatch that needs a second pair of eyes.

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the exact log line and your provider name.
