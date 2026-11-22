---
title: "Nginx Proxy Manager 502 After Restart: Resolve the Upstream Container Network"
description: "After restarting Nginx Proxy Manager, your proxy host returns 502 even though the upstream service runs on its direct port. Follow these checks to restore the proxy host."
seo_title: "Nginx Proxy Manager 502 After Restart: Fix Upstream Network"
seo_description: "After restarting Nginx Proxy Manager, your proxy host returns 502 even though the upstream service runs on its direct port. Follow these checks to restore the proxy host."
keywords:
  - "nginx proxy manager 502 after restart"
date: 2026-11-22T11:01:51+08:00
publishDate: 2026-11-22T11:01:51+08:00
draft: false
categories:
  - "Reverse Proxy & SSL"
category_slug: "reverse-proxy-ssl"
---

**nginx proxy manager 502 after restart**. You restarted Nginx Proxy Manager, and now the proxy host returns 502 although the upstream service is running on its direct port.

## Check the Upstream Container Network

Open the proxy host settings and note the exact forward hostname and port. Then, from the NPM container, test connectivity:

```bash
docker exec -it <npm_container> sh
nc -zv <forward_host> <forward_port>
```

If the connection is refused or times out, the upstream is not reachable from NPM's network. This is common after a restart when containers attach to different Docker networks or the upstream container's IP changed.

## Verify the Upstream Is on the Same Network

List the networks for both containers:

```bash
docker inspect <npm_container> --format '{{json .NetworkSettings.Networks}}'
docker inspect <upstream_container> --format '{{json .NetworkSettings.Networks}}'
```

If they share no network, attach the upstream to NPM's network:

```bash
docker network connect <npm_network> <upstream_container>
```

Then retry the proxy host. A 502 that clears confirms the network was the cause; a persistent 502 means the port or hostname is still wrong.

## Verification Test

From the NPM container, run `curl -I http://<forward_host>:<forward_port>`. A 200 or 301 response means the upstream is reachable. Reload the proxy host and confirm the browser no longer shows 502.

## Rollback and Recovery

If connecting networks breaks other services, disconnect the upstream with `docker network disconnect <npm_network> <upstream_container>` and restore the previous network configuration. Then revisit the forward hostname and port before making further changes.

If the 502 persists after these steps,

![A laptop displaying proxy host settings and a browser HTTPS test](/images/service-detail-960.webp "Nginx Proxy Manager host and certificate verification")

[Ask for help](/#consult) with the output of the connectivity tests.
