# Proxmox-rockylinux-Rhel-Homelab
Proxmox-rockylinux (Template) -Rhel-Homelab

# Proxmox Cloud-Init Template Guide

## Overview
This guide will help you set up a **Cloud-Init** template on **Proxmox Virtual Environment (PVE)**. Cloud-Init is a multi-distribution package that handles early VM initialization, including:
- Configuring hostname
- Setting up SSH keys
- Running early initialization scripts

## Prerequisites
- **Proxmox PVE installed**
- **Access to the PVE shell**

## Steps to Create a Cloud-Init Template

### 1. Downloading a Cloud-Init Image
We will use the **Debian 12 Cloud-Init** image:
```bash
  wget https://dl.rockylinux.org/pub/rocky/9/images/x86_64/Rocky-9-GenericCloud-Base.latest.x86_64.qcow2
```

### 2. Creating a Virtual Machine (VM)
Create a new VM with ID **9000**:
```bash
qm create 9000 --name debian12-cloudinit
```
> **Note**: Terraform is meant to manage the full lifecycle of the VM, so no further manual changes should be made to it.

### 3. Importing the Cloud-Init Image
Import the downloaded image into the newly created VM:
```bash
qm set 9000 --scsi0 local-lvm:0,import-from=/root/debian-12-genericcloud-amd64.qcow2
```

### 4. Creating a Template from the VM
Convert the VM to a template:
```bash
qm template 9000
```

### 5. Creating a Cloud-Init Snippet
Snippets allow additional configuration during VM creation.

#### 5.1 Create a Snippet Storage Directory
```bash
mkdir /var/lib/vz/snippets
```

#### 5.2 Create a Snippet for QEMU Guest Agent Installation
```bash
tee /var/lib/vz/snippets/qemu-guest-agent.yml <<EOF
#cloud-config
runcmd:
  - apt update
  - apt install -y qemu-guest-agent
  - systemctl start qemu-guest-agent
EOF
```

## Next Steps
- Clone VMs from the template using Terraform or manual cloning.
- Attach Cloud-Init snippets to customize VM provisioning.

## Notes
- Ensure your Cloud-Init template and snippets are stored on **accessible storage**.
- The cloned VMs may fail to start if the snippets are inaccessible.

## License
This project is licensed under the MIT License.


