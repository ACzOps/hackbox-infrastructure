# Service architecture
as of 15 may 2026

# mikrus.hackbox.cc
- wireguard server (jakie porty?)
- wg-portal
- prometheus-node-exporter

# bouncer.hackbox.cc
- wireguard relay
- prometheus-node-exporter
- firewall na opnsense
- unbound dns
- dhcp serwer

# supervisor.hackbox.cc
serwisy na klastrze K3s:
- klaster postgresql (jeden RW, dwie repliki RO)
- pgadmin (web ui)
- infisical (web ui)
- valkey
- valkey-ui (web ui)
- longhorn (web ui)
- fluxcd (web ui)
- garage
- traefik jako reverse proxy
- youtrack (web ui)
- prometheus (web ui)
- grafana (web ui)
- loki

w planach:
- authentik
