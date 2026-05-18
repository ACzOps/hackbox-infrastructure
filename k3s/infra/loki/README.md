## Requirements
Loki needs S3, so apply key and bucket creation commands on Garage:
```bash
# Create Loki key and save credentials
kubectl -n garage exec -it garage-0 -- /garage key create loki

# Add permision to create buckets
kubectl -n garage exec -it garage-0 -- /garage key allow --create-bucket loki
```
Create three buckets: `admin`, `chunks` and `ruler` with S3 credentials for Loki (saved from Garage account creation).
