# Keycloak

Production-ready Keycloak 23.0.0 Docker image with pre-built Quarkus optimization.

## Quick Start

Pull the image from GitHub Container Registry:

```bash
docker pull ghcr.io/rosenvold-technology/keycloak:latest
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
| `latest` | Latest build from main branch |
| `23.0.0` | Keycloak version |
| `23.0.0-<sha>` | Keycloak version with commit SHA |
| `v*.*.*` | Semantic version tags |

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
