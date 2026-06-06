# YouTrack runbook

## What to do when YouTrack is down and erased?

1. Find a detached storage volume claimed to `youtrack-backup`. There should be archives there that contain backups of YouTrack database. Mount it to a different host if any tries of mounting to YouTrack pods seem to fail. **Do not** put in in `maintenance` mode, because the option will result in attaching the volume without enabling the frontend. That option is to make sure no one can access the volume data when the volume is attached.
2. Extract file to a temporary folder of K3s control plane (i.e. `Supervisor` host in our case).
3. Copy backup archive to a new storage volume via `kubectl`:

```bash
kubectl cp /tmp/yt-backup.tar.gz youtrack/youtrack-0:/opt/youtrack/backups/
```

4. Refresh YouTrack upgrade page or just change subdirectory and come back to `/opt/youtrack/backups`. There should be an archive file with backup.
5. Confirm default values without changing anything. Don't mess with license name for fun, because it won't let you finish the upgrade. If you messed up, click `Default` and go.
