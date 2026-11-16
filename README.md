# NetBird Proxy with preflight support

> **Temporary workaround:** This repository provides a patched NetBird proxy until preflight handling can be configured directly in NetBird. If you’d like this behavior as a built-in option, please upvote or describe your usecase in [this discussion](https://github.com/netbirdio/netbird/discussions/7120).

This repository builds and publishes the NetBird proxy with a small patch for services using header authentication.

The patch forwards CORS preflight requests (`OPTIONS` requests containing both an `Origin` header and an `Access-Control-Request-Method` header) to the service without requiring header authentication for those requests. The actual request should still be authenticated.

## Container image

```text
ghcr.io/stakewa/netbird-proxy-preflight-patch:latest
```

Pull it with:

```bash
docker pull ghcr.io/stakewa/netbird-proxy-preflight-patch:latest
```

Use this image in place of the standard NetBird proxy image in your deployment configuration.
### Using Docker Compose

In your Compose file, keep your existing `proxy` service settings and replace its image:

```yaml
  proxy:
    image: ghcr.io/stakewa/netbird-proxy-preflight-patch:latest
    container_name: netbird-proxy
    ports:
      - 51820:51820/udp
    restart: unless-stopped
```

Then pull the image and recreate the service:

```bash
docker compose pull proxy
docker compose up -d proxy
```

## Notes

- Forwarding a preflight request does not itself add CORS headers. The service must return the appropriate CORS response headers.
- The image is maintained independently of NetBird. Review and test updates before using them in production.
- If you’d like this behavior to be available as an option, please upvote [this discussion](https://github.com/netbirdio/netbird/discussions/7120).