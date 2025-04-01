# k8s-gitops-sandbox

Sandbox for k8s-gitops

## setup

See [setup/README.md](setup/README.md) for instructions on how to set up & bootstrap the cluster.

## TODOs

- [x] system-upgrade-controller
- [x] test lb & tailscale ingress with *.t.eviljungle.com domain
- [ ] test volsync backups & restores
- [x] ~~test & configure NUT UPS monitoring stuff with talos~~ - will tackle this with pods and some other solution
- [x] ensure that test certs from cert-manager are generated & used
- [ ] test rook volume resizing
- [ ] test talos-backup & restore
- [ ] test automatic device discovery & movement (probably use coral to do this)
- [ ] test frigate end-to-end
- [ ] implement tailscale control plane access
- [ ] test rook object storage
- [ ] copy & refactor all monitoring apps (e.g. loki/vector/grafana/influxdb/etc)
- [ ] copy & refactor all 'default' apps, including database (cnpg?)
- [ ] develop migration plan and runbook for actual cutover
- [ ] address all outstanding TODOs in the files in this repo
