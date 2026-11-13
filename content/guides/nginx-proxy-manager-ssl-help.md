---
title: "Nginx Proxy Manager 502: Check the Upstream First"
description: "A practical first-pass check for Nginx Proxy Manager HTTPS Help."
date: 2026-11-13
---
<p>A 502 is usually the upstream, not the certificate. I verify DNS, proxy host, and service health in order.</p>
<h2>1. Reproduce the exact failure</h2>
<p>Write down what works, what does not, and the last change. Test from the same device and network where the problem happens. A second device is useful only as a comparison, not as proof.</p>
<h2>2. Check the layer before the app</h2>
<p>Confirm the host is reachable, the account or device is allowed, and the local client can see the service. Then test the app setting. Changing three layers at once makes the result impossible to interpret.</p>
<h2>3. Make one change and retest</h2>
<p>Record the old value, apply one change, retest, and keep the result. If the change does not fix it, roll it back before trying the next path.</p>
<h2>4. Handover test</h2>
<p>Repeat the successful test after a restart or reconnect. A fix that only works in the current session is not ready for handover.</p>
<p><a href="/">Back to the service page</a> · <a href="mailto:hello@handytasklab.com?subject=Nginx%20Proxy%20Manager%20HTTPS%20Help%20question&body=Source%3A%20nginx-proxy-manager-ssl-help">Ask about this exact symptom</a></p>
