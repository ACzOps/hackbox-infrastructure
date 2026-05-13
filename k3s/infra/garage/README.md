## Important info
After successful Garage installation from Helm, initial layout has to be applied by these commands:
```bash
# Check nodes
kubectl -n garage exec -it garage-0 -- /garage status

# Apply command for each node
kubectl -n garage exec -it garage-0 -- /garage layout assign <id> -z k3s -c <disk_size> -t <tag>

# Apply layout
kubectl -n garage exec -it garage-0 -- /garage layout apply
```
