### Important note
For the cluster here there was a secret created manually, named `postgresql-cluster-infisical`. Example below:
```yaml
---
apiVersion: v1
kind: Secret
type: kubernetes.io/basic-auth
metadata:
  name: postgresql-cluster-infisical
  namespace: postgresql
data:
  username: <base64_login>
  password: <base64_password>
```
