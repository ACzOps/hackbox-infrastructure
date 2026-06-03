# Important information

## Authentik secret

Generate a generic secret for Authentik before launching Helm chart:

```bash
kubectl -n authentik create secret generic authentik-secret --from-literal=password=$(openssl rand -hex 32)
```

## PostgreSQL credentials

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: authentik-postgresql-creds
  namespace: authentik
type: Opaque
stringData:
  username: <db_user>
  password: <db_password>

```
