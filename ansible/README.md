# Ansible — Hackbox.cc infrastructure

Playbooks and roles used to provision bare-metal hosts and bootstrap the k3s control plane. Run the commands below from **this directory** (the `ansible/` folder at the repository root).

## Requirements

Install required Ansible collections before running playbooks:

`ansible-galaxy collection install -r requirements.yml`

## Playbooks

Playbooks live under [`playbooks/`](playbooks/).

- [`00-debian-hardening.yml`](playbooks/00-debian-hardening.yml) — initial Debian bootstrap: `service` user / sudo policy, authorized keys, and SSH server hardening. Uses `become_method: su` as root; typically run **once** per host, then rely on sudo-based playbooks afterward.
- [`01-debian-setup.yml`](playbooks/01-debian-setup.yml) — baseline packages on `debian_hosts` via `apt` (sudo-based).
- [`01-wireguard.yml`](playbooks/01-wireguard.yml) — WireGuard and WireGuard Portal on `wireguard` inventory hosts.
- [`02-k3s-setup.yml`](playbooks/02-k3s-setup.yml) — installs K3s using roles from the [`k3s-ansible`](https://github.com/k3s-io/k3s-ansible) collection (`k3s.orchestration.*`; pinned in [`requirements.yml`](requirements.yml)), plus local [`k3s_setup`](roles/k3s_setup/) tasks (preflight, cluster tools, Flux CLI, and similar). Use `--skip-tags preflight` when reinstalling k3s, as noted in the playbook.
- [`03-k3s-provisioning.yml`](playbooks/03-k3s-provisioning.yml) — post-install cluster bits via [`k3s_provisioning`](roles/k3s_provisioning/) (Flux Operator install, cert-manager secrets).

## Roles

Custom roles live under [`roles/`](roles/).

- [`debian_sudo`](roles/debian_sudo/) — `service` user, sudoers, and authorized SSH keys for Debian hosts.
- [`sshd_hardening`](roles/sshd_hardening/) — OpenSSH server hardening options.
- [`debian_packages`](roles/debian_packages/) — baseline `apt` packages on Debian hosts.
- [`wireguard`](roles/wireguard/) — WireGuard interface and peers.
- [`wireguard_portal`](roles/wireguard_portal/) — WireGuard Portal stack (systemd units, Caddy, templates).
- [`k3s_setup`](roles/k3s_setup/) — preflight checks, pip/Helm-related prep on the server, `k9s`, Flux CLI install, and verification tasks around the upstream K3s install.
- [`k3s_provisioning`](roles/k3s_provisioning/) — Flux Operator (Helm) and cert-manager secret material applied from the control-plane host.

## Troubleshooting

### Host Key checking error

If you see an error like `Using a SSH password instead of a key is not possible because Host Key checking is enabled`, preload host fingerprints into your local `known_hosts` file:

`ansible-inventory -i inventory/hosts.yml --list | jq -r "._meta.hostvars[] | .ansible_host" | xargs -0 ssh-keyscan -H >> ~/.ssh/known_hosts`

For the WireGuard host on a custom SSH port:

`ssh-keyscan -H -p 10122 wg.hackbox.cc >> ~/.ssh/known_hosts`

The `-H` flag hashes hostnames in the output before writing them to `known_hosts`, which helps avoid exposing plaintext hostnames if the file is shared.

### Limiting hosts

Every host has a different `service` account password for security reasons, so limit Debian bootstrap playbooks to one host when needed, for example: `ansible-playbook --limit <hostname> playbooks/00-debian-hardening.yml -u service -k -K` (or `playbooks/01-debian-setup.yml` with the same limit pattern once sudo works).

### Timeout waiting for privilege escalation prompt error

Besides flag `-k` for user password use also `-K` flag to ask for root password in `ansible-playbook` command.

### Vault password flags

When using encrypted variables, remember to pass a vault password option to `ansible-playbook`: `--vault-password-file <path>` or `--ask-vault-pass`.
