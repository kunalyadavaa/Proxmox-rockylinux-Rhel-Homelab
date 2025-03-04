# Proxmox-rockylinux-Homelab
Proxmox-rockylinux (Template) -Rhel-Homelab

# Proxmox Cloud-Init Template Guide

## Overview
This guide will help you set up a **Cloud-Init** template on **Proxmox Virtual Environment (PVE)**. Cloud-Init is a multi-distribution package that handles early VM initialization, including:
- Configuring hostname
- Setting up SSH keys
- Running early initialization scripts
- Deploying Cloud-Init Templates

## Prerequisites
- **Proxmox PVE installed**
- **Access to the PVE's shell**

## Steps to Create a Cloud-Init Template

### 1. Downloading a Cloud-Init Image
We will use the **RockyLinux Cloud-Init** image:
```bash
  wget https://dl.rockylinux.org/pub/rocky/9/images/x86_64/Rocky-9-GenericCloud-Base.latest.x86_64.qcow2
```

### 2. Creating a Virtual Machine (VM)
Create a new VM with ID **9005**:
```bash
qm create 9005 --name rockylinux-cloudinit
```
> **Note**: Terraform is meant to manage the full lifecycle of the VM, so no further manual changes should be made to it.

### 3. Importing the Cloud-Init Image
Import the downloaded image into the newly created VM:
```bash
qm set 9005 --scsi0 local-lvm:0,import-from=/root/Rocky-9-GenericCloud-Base.latest.x86_64.qcow2
```

### 4. Creating a Template from the VM
Convert the VM to a template:
```bash
qm template 9005
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
  - dnf update
  - dnf install -y qemu-guest-agent
  - systemctl start qemu-guest-agent
EOF
```

#### Deploying Cloud-Init Templates
screenshot/gui-cloudinit-config.png

You can easily deploy such a template by cloning:

```bash
qm clone 9005 9006 --name rockylinux
```

Then configure the SSH public key used for authentication, and configure the IP setup:

```bash
qm set 123 --sshkey ~/.ssh/id_rsa.pub
qm set 123 --ipconfig0 ip=10.0.10.123/24,gw=10.0.10.1
```

You can also configure all the Cloud-Init options using a single command only. We have simply split the above example to separate the commands for reducing the line length. Also make sure to adopt the IP setup for your specific environment.

#### IMPORTANT Documents To Read


 https://pve.proxmox.com/wiki/Cloud-Init_Support
=
## Next Steps
- Clone VMs from the template using Terraform or manual cloning.
- Attach Cloud-Init snippets to customize VM provisioning.

## Notes
- Ensure your Cloud-Init template and snippets are stored on **accessible storage**.
- The cloned VMs may fail to start if the snippets are inaccessible.

## License
This project is licensed under the MIT License.


