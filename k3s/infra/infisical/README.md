### Important note
For this Infisical installation there was a secret created manually, named `infisical-secrets` with multiple important keys.
Example is shown below:
```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: infisical-secrets
  namespace: infisical
type: Opaque
stringData:
  AUTH_SECRET: <random 32-length string>
  ENCRYPTION_KEY: <openssl rand -hex 16>
  DB_CONNECTION_URI: "postgresql://infisical:<password>@postgresql-cluster-rw.postgresql:5432/infisical?sslmode=require"
  REDIS_URL: "rediss://:password@valkey.valkey:6379"
  SITE_URL: "https://infisical.k3s.hackbox.cc"
  OTEL_TELEMETRY_COLLECTION_ENABLED: "true"
  OTEL_EXPORT_TYPE: "prometheus"
```
