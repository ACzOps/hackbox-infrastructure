# Hackbox.cc infrastructure repository

Provisioning scripts, playbooks and recipes for Hackbox.cc infrastructure

## Requirements

Install required Ansible collections before running playbooks:

`ansible-galaxy collection install -r requirements.yml`

## Playbooks

- `playbooks/debian_setup.yml` - applies baseline Debian host provisioning to all inventory hosts (SSH key setup, base packages, and sudo policy for the `sudo` group). Launch it only once, since it uses `su` method or change to `sudo` method if needed to launch again.
- `playbooks/k3s_setup.yml` - imports the upstream K3s orchestration playbook to install and configure the K3s cluster.

## Roles

- `roles/debian_packages` - installs baseline Debian packages via `apt`.
- `roles/debian_setup` - provisions Debian hosts by configuring authorized SSH keys, adding the `service` user to `sudo`, and enforcing passwordless sudo for the `sudo` group.
- `roles/k3s_setup` - downloads and runs the K3s installer for control-plane and node hosts with role defaults for token and control-plane endpoint.

## Troubleshooting

### Host Key checking error

If you see an error like `Using a SSH password instead of a key is not possible because Host Key checking is enabled`, preload host fingerprints into your local `known_hosts` file:

`ansible-inventory -i inventory/hosts.yml --list | jq -r "._meta.hostvars[] | .ansible_host" | xargs -0 ssh-keyscan -H >> ~/.ssh/known_hosts`

For the WireGuard host on a custom SSH port:

`ssh-keyscan -H -p 10122 wg.hackbox.cc >> ~/.ssh/known_hosts`

The `-H` flag hashes hostnames in the output before writing them to `known_hosts`, which helps avoid exposing plaintext hostnames if the file is shared.

### Limiting hosts

Every host has a different `service` account password for security reasons, so please limit scope of `debian_setup` playbook to a desired host via `ansible-playbook --limit <hostname> playbooks/debian_setup.yml -u service -k -K` command.

### Timeout waiting for privilege escalation prompt error

Besides flag `-k` for user password use also `-K` flag to ask for root password in `ansible-playbook` command.

### Vault password flags

When using encrypted variables, remember to pass a vault password option to `ansible-playbook`: `--vault-password-file <path>` or `--ask-vault-pass`.
