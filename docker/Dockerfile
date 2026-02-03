# Keycloak 23.0.0 Production-Ready Dockerfile
# Multi-stage build for optimized Quarkus-based Keycloak

ARG KEYCLOAK_VERSION=23.0.0

# =============================================================================
# Stage 1: Builder - Pre-build Quarkus optimization
# =============================================================================
FROM quay.io/keycloak/keycloak:${KEYCLOAK_VERSION} AS builder

# Configure build-time features
ENV KC_HEALTH_ENABLED=true
ENV KC_METRICS_ENABLED=true
ENV KC_HTTP_MANAGEMENT_PORT=9000

WORKDIR /opt/keycloak

# Build optimized Keycloak distribution
# This pre-compiles Quarkus for faster startup in production
RUN /opt/keycloak/bin/kc.sh build

# =============================================================================
# Stage 2: Production image
# =============================================================================
FROM quay.io/keycloak/keycloak:${KEYCLOAK_VERSION}

# Copy optimized build from builder stage
COPY --from=builder /opt/keycloak/lib/quarkus/ /opt/keycloak/lib/quarkus/

# Uncomment to add custom themes
# COPY --chown=keycloak:keycloak themes/ /opt/keycloak/themes/

# Uncomment to add custom providers (SPIs)
# COPY --chown=keycloak:keycloak providers/ /opt/keycloak/providers/

WORKDIR /opt/keycloak

# Runtime configuration
ENV KC_HEALTH_ENABLED=true
ENV KC_METRICS_ENABLED=true
ENV KC_HTTP_MANAGEMENT_PORT=9000

# Expose ports:
# 8080  - HTTP
# 8443  - HTTPS
# 9000  - Management (health/metrics)
EXPOSE 8080 8443 9000

# Health check using management endpoint
HEALTHCHECK --interval=30s --timeout=10s --start-period=60s --retries=3 \
    CMD exec 3<>/dev/tcp/localhost/9000 && echo -e 'GET /health/ready HTTP/1.1\r\nHost: localhost\r\nConnection: close\r\n\r\n' >&3 && cat <&3 | grep -q '"status":"UP"'

# Re-declare ARG for use in this stage
ARG KEYCLOAK_VERSION

# OCI Image Labels
LABEL org.opencontainers.image.title="Keycloak"
LABEL org.opencontainers.image.description="Production-ready Keycloak identity and access management"
LABEL org.opencontainers.image.version="${KEYCLOAK_VERSION}"
LABEL org.opencontainers.image.vendor="Rosenvold Technology"
LABEL org.opencontainers.image.source="https://github.com/rosenvold-technology/keycloak"
LABEL org.opencontainers.image.licenses="Apache-2.0"

# Default entrypoint starts Keycloak in production mode
# Override with 'start-dev' for development
ENTRYPOINT ["/opt/keycloak/bin/kc.sh"]
