## Important info
After successful Garage installation from Helm, initial layout has to be applied by these commands:
```bash
# Check nodes
kubectl -n garage exec -it garage-0 -- /garage status

# Apply command for each node
kubectl -n garage exec -it garage-0 -- /garage layout assign <id> -z k3s -c <disk_size> -t <tag>

# Apply layout (check if version 1 is correct by typing: garage layout show)
kubectl -n garage exec -it garage-0 -- /garage layout apply --version 1

# Set new account
kubectl -n garage exec -it garage-0 -- /garage key create <name>

# Allow new account to create buckets
kubectl -n garage exec -it garage-0 -- /garage key allow <name> --create-bucket
```
