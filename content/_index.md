---
title: Nginx Proxy Manager SSL Help
seo_title: Nginx Proxy Manager 502 and SSL Help
seo_description: Remote help for Nginx Proxy Manager 502 errors, upstream reachability, DNS, and HTTPS verification.
keywords:
- nginx proxy manager 502
- nginx proxy manager ssl not working
- reverse proxy upstream unreachable
hero_kicker: REVERSE PROXY AND HTTPS
hero_h1: Nginx Proxy Manager shows a 502?
hero_lead: Check whether the upstream service is reachable before blaming the certificate, then verify HTTPS for one hostname and one service.
hero_proof:
- One hostname and one upstream service
- Upstream checked before certificate changes
- HTTPS verified from a client browser
hero_alt: Mini server and laptop showing an Nginx Proxy Manager dashboard
hero_title: Nginx Proxy Manager mini server and laptop
body_alt: A laptop displaying proxy host settings and a browser HTTPS test
body_title: Nginx Proxy Manager host and certificate verification
body_caption: HTTPS is only finished when the hostname reaches the intended service with a valid certificate.
failures_kicker: THE THREE FAILURES WE SEE
failures_heading: What is going wrong?
test_kicker: HOW WE TEST
test_heading: Find the failing layer, then prove the fix.
scope_kicker: SERVICE SCOPE
scope_heading: What is included
scope_note: The session covers one agreed device and one verified path. Anything outside the written scope is quoted separately before work starts.
faq_kicker: FAQ
faq_heading: Before you book
consult_heading: Tell us what is happening.
consult_lead: Send the device, software, exact symptom, and what you have already tried. We will confirm scope and a fixed quote before booking.
form_placeholder: Device, software version, exact symptom, and what you have tried.
first_guide_url: /guides/nginx-proxy-manager-ssl-help/
symptoms:
- title: 502 Bad Gateway
  text: The proxy host resolves, but the browser returns 502 or 504 instead of the service.
- title: Certificate warning
  text: HTTPS loads with a name mismatch, expired certificate, or untrusted issuer warning.
- title: Works by IP, not hostname
  text: The service is reachable on the LAN address but not through the proxy hostname.
checks:
- title: Test the upstream directly
  text: From the proxy container or host, request the service by its internal IP and port. If that fails, fix the service before touching TLS.
- title: Confirm DNS and proxy host
  text: Check that the hostname points to the proxy and that the proxy host's forward hostname, port, and scheme match the service.
- title: Inspect the certificate
  text: Confirm the certificate covers the exact hostname and is not expired. A valid certificate cannot fix an unreachable upstream.
- title: Verify from a client
  text: Open the HTTPS URL in a clean browser profile, check the certificate chain, and confirm the expected application loads.
included:
- One hostname and one upstream service
- DNS, proxy host, and upstream checks
- Certificate and HTTPS verification
- Rollback notes for proxy changes
excluded:
- Issuing a paid certificate
- Building a new application
- Router or ISP changes without approval
- Guaranteeing third-party DNS propagation time
faqs:
- q: Is a 502 always a certificate problem?
  a: No. A 502 usually means the proxy cannot reach or get a valid response from the upstream; TLS is a separate layer.
- q: Can you fix my DNS?
  a: We verify the DNS record and explain the required change. The domain owner makes the actual registrar or DNS edit.
- q: Will you restart my whole server?
  a: Only the specific service or proxy component after approval, and only when the evidence points there.
---
