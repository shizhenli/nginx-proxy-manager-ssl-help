---
title: 'Nginx Proxy Manager 502: Check the Upstream Before the Certificate'
description: A first-pass Nginx Proxy Manager 502 diagnosis that separates upstream reachability, DNS, and certificate problems.
seo_title: Nginx Proxy Manager 502? Upstream and SSL Checks
seo_description: A first-pass Nginx Proxy Manager 502 diagnosis that separates upstream reachability, DNS, and certificate problems.
keywords:
- nginx proxy manager 502
- nginx proxy manager ssl not working
- reverse proxy upstream unreachable
date: '2026-11-13'
draft: false
categories:
- Reverse Proxy & SSL
category_slug: reverse-proxy-ssl
---

<p>A 502 from Nginx Proxy Manager means the request reached the proxy but the upstream answer was missing or invalid. Test that path before changing TLS.</p>
<h2>Before you start</h2><ul>
<li>The proxy hostname and upstream IP/port</li>
<li>Access to Nginx Proxy Manager logs</li>
<li>A client browser and a terminal on the proxy network</li>
<li>Permission to restart one service if needed</li>
</ul>
<h2>Step-by-step checks</h2>
<h3>Step 1: Read the proxy error</h3>
<p>Open the proxy host's logs and record the upstream address, port, and exact error. A connection refused, timeout, and DNS failure point to different layers.</p>
<p><strong>Check:</strong> The log names the upstream and failure type.</p>
<h3>Step 2: Request the upstream directly</h3>
<p>From the proxy host, use curl or a browser to request the internal service address. Test the same scheme and path the proxy uses.</p>
<p><strong>Check:</strong> The upstream returns the expected HTTP response without the proxy.</p>
<h3>Step 3: Match the proxy host settings</h3>
<p>Compare forward hostname/IP, port, scheme, and websocket settings with the service. Correct one field at a time and save the host.</p>
<p><strong>Check:</strong> The proxy host points to the service that answered the direct test.</p>
<h3>Step 4: Check DNS and certificate separately</h3>
<p>Resolve the public hostname, confirm it reaches the proxy, then inspect the certificate's subject, SANs, and dates. Do not reissue a certificate to fix a 502.</p>
<p><strong>Check:</strong> DNS points to the proxy and the certificate matches the hostname.</p>
<h3>Step 5: Verify HTTPS from a client</h3>
<p>Open the hostname in a clean browser, confirm the certificate chain, and load a known page. Repeat after a proxy reload.</p>
<p><strong>Check:</strong> The intended app loads over HTTPS without a certificate warning.</p>
<h2>Verify the result</h2>
<p>The public hostname reaches the intended upstream, the certificate is trusted for that name, and a second browser profile gets the same result.</p>
<h2>Rollback</h2>
<p>Keep the previous proxy-host values and certificate reference. Restore them if a change makes the host unreachable.</p>
<p><a href="/#consult">Ask for help with this exact symptom</a> · <a href="/guides/">All troubleshooting guides</a></p>
