# k8s-gitops-sandbox

Sandbox for k8s-gitops

## setup

See [setup/README.md](setup/README.md) for instructions on how to set up & bootstrap the cluster.

## TODOs

- [x] system-upgrade-controller
- [x] test lb & tailscale ingress with *.t.eviljungle.com domain
- [x] test volsync backups & restores
- [x] ~~test & configure NUT UPS monitoring stuff with talos~~ - will tackle this with pods and some other solution
- [x] ensure that test certs from cert-manager are generated & used
- [x] test rook volume resizing
- [x] test talos-backup & restore
- [x] test automatic device discovery & movement (probably use coral to do this)
- [x] test frigate end-to-end
- [x] implement tailscale control plane access
- [x] test rook object storage
- [x] copy & refactor all monitoring apps (e.g. loki/vector/grafana/influxdb/etc)
- [x] test database restore from real copy
- [x] copy & refactor all 'default' apps, including database (cnpg?)
- [x] develop migration plan and runbook for actual cutover
- [ ] address all outstanding TODOs in the files in this repo
