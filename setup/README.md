# cluster setup with talos

## Physical devices (amd64)

## Proxmox

### control plane node

* MAC address is hard-coded so that the dhcp server will assign the appropriate IP address

```shell
sudo qm create 501 \
  --name k8s-0 \
  --ostype l26 \
  --machine q35 \
  --memory 8192 \
  --cores 4 \
  --cpu host \
  --agent "enabled=1,fstrim_cloned_disks=1" \
  --bios ovmf \
  --net0 "model=virtio,macaddr=BC:24:11:73:58:F0,bridge=brk8s20" \
  --scsihw virtio-scsi-single \
  --efidisk0 "ssdtank-proxmox:1,efitype=4m" \
  --scsi0 "ssdtank-proxmox:200,backup=0,iothread=1,ssd=1,discard=on" \
  --ide2 tank-proxmox:iso/metal-amd64.iso,media=cdrom
```

### worker node

* MAC address is hard-coded so that the dhcp server will assign the appropriate IP address
* GPU passthrough (`--hostpci0`) is enabled for the Intel GVT-g device
* additional scsi disk (`/dev/disk/by-id/wwn-0x5002538c000fc6fd`) is added for the ceph OSD

```shell
sudo qm create 502 \
  --name k8s-a \
  --ostype l26 \
  --machine q35 \
  --memory 32768 \
  --cores 4 \
  --cpu host \
  --agent "enabled=1,fstrim_cloned_disks=1" \
  --bios ovmf \
  --net0 "model=virtio,macaddr=BC:24:11:F1:D5:9B,bridge=brk8s20" \
  --scsihw virtio-scsi-single \
  --efidisk0 "ssdtank-proxmox:1,efitype=4m" \
  --scsi0 "ssdtank-proxmox:200,backup=0,iothread=1,ssd=1,discard=on" \
  --ide2 tank-proxmox:iso/metal-amd64.iso,media=cdrom \
  --hostpci0 "0000:00:02.0,mdev=i915-GVTg_V5_4" \
  --scsi1 "/dev/disk/by-id/wwn-0x5002538c000fc6fd,backup=0,iothread=1,ssd=1,discard=on"
```

## talos

(run from the repo root)

Use talhelper to generate the config files in the `clusterconfig` directory.

```shell
task talos:generate-clusterconfig
```

Boostrap the control-plane node

```shell
# talosctl apply-config --talosconfig=./clusterconfig/talosconfig --nodes=10.2.0.50 --file=./clusterconfig/home-k8s-0.yaml --insecure
# wait till the node reboots and is healthy
task talos:apply-clusterconfig INSECURE=true
talosctl bootstrap --talosconfig=./talos/clusterconfig/talosconfig --nodes=10.2.0.50
```

Bootstrap the worker node

```shell
talosctl apply-config --talosconfig=./clusterconfig/talosconfig --nodes=10.2.0.51 --file=./clusterconfig/home-k8s-a.yaml --insecure
```
