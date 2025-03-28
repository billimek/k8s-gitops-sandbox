# k8s-gitops-sandbox

Sandbox for k8s-gitops

## setup

See [setup/README.md](setup/README.md) for instructions on how to set up & bootstrap the cluster.

## TODOs

- [ ] system-upgrade-controller
- [ ] test lb & external ingress with *.t.eviljungle.com domain
- [ ] test lb & tailscale ingress with *.ts.eviljungle.com domain
- [ ] test volsync backups & restores
- [ ] ensure that test certs from cert-manager are generated & used
- [ ] test rook volume resizing
- [ ] test automatic device discovery & movement (probably use coral to do this)
- [ ] test frigate end-to-end
- [ ] implement tailscale control plane access
- [ ] test rook object storage
- [ ] copy & refactor all monitoring apps (e.g. loki/vector/grafana/influxdb/etc)
- [ ] copy & refactor all 'default' apps, including database (cnpg?)
- [ ] develop migration plan and runbook for actual cutover
- [ ] address all outstanding TODOs
