# Keycloak

Production-ready Keycloak Docker images with pre-built Quarkus optimization.

**Available versions:** 23.0.0, 26.5.2

## Quick Start

Pull the image from GitHub Container Registry:

```bash
# Latest version (26.5.2)
docker pull ghcr.io/rosenvold-technology/keycloak:latest

# Or specific version
docker pull ghcr.io/rosenvold-technology/keycloak:26.5.2
docker pull ghcr.io/rosenvold-technology/keycloak:23.0.0
```

### Development Mode

Run Keycloak in development mode (no HTTPS required):

```bash
docker run -p 8080:8080 -p 9000:9000 \
  -e KEYCLOAK_ADMIN=admin \
  -e KEYCLOAK_ADMIN_PASSWORD=admin \
  ghcr.io/rosenvold-technology/keycloak:latest start-dev
```

Access Keycloak at http://localhost:8080

### Production Mode

Run Keycloak in production mode (requires HTTPS configuration):

```bash
docker run -p 8443:8443 -p 9000:9000 \
  -e KEYCLOAK_ADMIN=admin \
  -e KEYCLOAK_ADMIN_PASSWORD=admin \
  -e KC_HOSTNAME=your-domain.com \
  -v /path/to/certs:/opt/keycloak/certs \
  ghcr.io/rosenvold-technology/keycloak:latest start \
  --https-certificate-file=/opt/keycloak/certs/cert.pem \
  --https-certificate-key-file=/opt/keycloak/certs/key.pem
```

## Image Tags

| Tag | Description |
|-----|-------------|
| `latest` | Latest build (26.5.2) from main branch |
| `26.5.2` | Keycloak 26.5.2 |
| `26.5.2-<sha>` | Keycloak 26.5.2 with commit SHA |
| `23.0.0` | Keycloak 23.0.0 (legacy) |
| `23.0.0-<sha>` | Keycloak 23.0.0 with commit SHA |

## Exposed Ports

| Port | Description |
|------|-------------|
| 8080 | HTTP |
| 8443 | HTTPS |
| 9000 | Management (health/metrics) |

## Health Checks

Check service health:

```bash
curl http://localhost:9000/health/ready
curl http://localhost:9000/health/live
curl http://localhost:9000/metrics
```

## Customization

### Custom Themes

Place theme files in the `themes/` directory and uncomment the COPY instruction in `docker/Dockerfile`:

```dockerfile
COPY --chown=keycloak:keycloak themes/ /opt/keycloak/themes/
```

### Custom Providers (SPIs)

Place provider JARs in the `providers/` directory and uncomment the COPY instruction in `docker/Dockerfile`:

```dockerfile
COPY --chown=keycloak:keycloak providers/ /opt/keycloak/providers/
```

## Building Locally

```bash
docker build -f docker/Dockerfile -t keycloak:local .
```

## License

Apache-2.0
