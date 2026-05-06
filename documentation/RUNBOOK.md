# Runbook for provisioning procedures

## Temporary problem listing in runbook style

### Problem 1: YouTrack and Longhorn ext4 fs

There is a problem when setting up YouTrack, because Setup expects an empty directory on `/opt/youtrack/data` during initialization, but inside it there is `lost+found` folder (default thing on ext4 filesystem) which creates an error. Temporary solution is to execute bash on `youtrack-install-0` pod and delete this folder, but it will be much better to swtich to XFS in Longhorn `StorageClass`.

Link to [that issue](https://github.com/longhorn/longhorn/issues/1580)
