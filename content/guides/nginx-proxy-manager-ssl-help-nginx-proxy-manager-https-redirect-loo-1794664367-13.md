---
title: "Nginx Proxy Manager HTTPS Redirect Loop: Check the Upstream Scheme Before You Change Anything"
description: "Browser bouncing between HTTP and HTTPS in Nginx Proxy Manager? Check the upstream scheme setting first, then verify with curl and roll back safely if needed."
seo_title: "Nginx Proxy Manager HTTPS Redirect Loop: Fix the Upstream Scheme"
seo_description: "Browser bouncing between HTTP and HTTPS in Nginx Proxy Manager? Check the upstream scheme setting first, then verify with curl and roll back safely if needed."
keywords:
  - "nginx proxy manager https redirect loop"
date: 2026-11-21T14:55:35+08:00
publishDate: 2026-11-21T14:55:35+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

Your browser keeps bouncing between HTTP and HTTPS until it gives up with too many redirects, and you are stuck at the https redirect loop step before any controlled diagnosis.

## Start with the upstream scheme

In Nginx Proxy Manager, open the proxy host for the affected service and look at the Scheme field under the Forward Hostname / IP section. If your backend already serves HTTPS, this field must be https, not http. When NPM forwards an HTTP request to a backend that itself redirects to HTTPS, the backend sends you back to NPM, NPM redirects again, and the cycle never ends.

## Interpret the result

If the Scheme reads http while the backend listens on 443 with its own redirect, that mismatch is your loop. Change Scheme to https, keep the backend port correct, and save. If the Scheme already matches the backend, do not change it blindly; move on and compare the backend's own redirect rule with the SSL tab's Force SSL setting, because two redirect layers produce the same symptom.

## Verification test

From a terminal, run curl -I http://your.domain and curl -I https://your.domain. A healthy setup returns one 301 or 308 to HTTPS on the first call and a 200 or backend response on the second. Repeated 301s in either output mean the loop persists.

## Rollback note

Before editing, note the original Scheme and port values. If the change makes the service unreachable, restore those exact values and save; NPM applies configuration immediately, so reverting is quick.

If the loop survives a correct scheme match, the remaining cause is usually a backend redirect rule fighting NPM.

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with your curl output and proxy host settings for a focused review of this **nginx proxy manager https redirect loop**.
