# cluster setup with talos

## Physical devices (amd64)

TBD

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
  * Because I will probably forget this, the `i915-GVTg_V5_1` mdev device is possible to use only because the BIOS setting for the GPU memory were increased from the default value to the max value.
  * The benefit of using a 'larger' (smaller suffix number) mdev device is that it has more memory/compute capacity than the smaller mdev devices and will improve transocindg performance. See [here](https://blog.ktz.me/why-i-stopped-using-intel-gvt-g-on-proxmox/) and [here](https://github.com/intel/gvt-linux/wiki/GVTg_Setup_Guide#53-create-vgpu-kvmgt-only) for more information
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
  --hostpci0 "0000:00:02.0,mdev=i915-GVTg_V5_1,pcie=1" \
  --scsi1 "/dev/disk/by-id/wwn-0x5002538c000fc6fd,backup=0,iothread=1,ssd=1,discard=on"
```

## talos setup & bootstrapping

(run from the repo root)

Use talhelper to generate the config files in the `clusterconfig` directory.

```shell
task talos:generate-clusterconfig
```

Bootstrap the talos nodes. It may take some time for the cluster to be ready.

```shell
task k8s-bootstrap:talos
```

## kubernetes setup & bootstrapping

Bootstrap the kubernetes cluster with required prerequisites (cilium CNI, CRDs, flux, etc).

```shell
task k8s-bootstrap:apps
```

## TODOs

* Handle volsync CRDs
* Handle snapshot-controller installation prerequisite for volsync
* Handle ingress2gateway?
* 