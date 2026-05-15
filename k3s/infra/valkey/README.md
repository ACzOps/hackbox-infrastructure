# Important note

For this Valkey installation there was a secret created manually, named `valkey-users` with keys `admin-password`, `readonly-password`, and `infisical-password`.
Example below:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: valkey-users
  namespace: valkey
type: Opaque
data:
  admin-password: <base64_pass>
  readonly-password: <base64_pass>
  infisical-password: <base64_pass>
```
