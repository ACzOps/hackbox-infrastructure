## Requirements
Loki needs S3, so apply key and bucket creation commands on Garage:
```bash
# Create Loki key and save credentials
kubectl -n garage exec -it garage-0 -- /garage key create loki

# Add permision to create buckets
kubectl -n garage exec -it garage-0 -- /garage key allow --create-bucket loki
```
Create two buckets: `chunks` and `ruler` from Loki account.

### Secret
Create secret and fill with correct creds using this template:
```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: loki-s3
  namespace: loki
type: Opaque
stringData:
  s3-endpoint: "https://s3.k3s.hackbox.cc"
  s3-access-id: ""
  s3-access-key: ""
```
