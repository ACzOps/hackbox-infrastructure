# Important info

## Generate secret with PostgreSQL credentials

Here's scaffold:

```yaml
---
apiVersion: v1
kind: Secret
type: kubernetes.io/basic-auth
metadata:
  name: gitlab-postgres
  namespace: gitlab
stringData:
  password: <password_in_plaintext>
```

## Generate secret with Valkey credentials

Scaffold is almost the same:

```yaml
---
apiVersion: v1
kind: Secret
type: kubernetes.io/basic-auth
metadata:
  name: gitlab-valkey
  namespace: gitlab
stringData:
  password: <password_in_plaintext>
```
