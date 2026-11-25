
# TurnKey File Server LXC
#Those who dont know [Turnkey linux](https://www.turnkeylinux.org/) is  library of pre-packaged, ready-to-deploy software templates to run them as Linux containers(LXC)

## Overview

Deployed TurnKey File Server as a centralized network storage service within my Proxmox infrastructure.

The service provides shared storage over SMB/CIFS, allowing infrastructure components and authorized systems to access centralized data through a common network share.

## Architecture

TurnKey File Server runs as an LXC container on Proxmox VE.

Storage is provisioned and managed at the Proxmox level, then exposed to the container through an LXC mount point:

- **Container:** TurnKey-FS
- **Storage mount point:** `/mnt/smb-share`
- **File sharing protocol:** SMB/CIFS
- **Access management:** Role-based access control (RBAC)
  
A Proxmox-managed storage volume is mapped into the LXC container using a mount point. This allows the file server to provide network-accessible storage without requiring the storage volume to be independently managed inside the container.

  <img width="1138" height="343" alt="image" src="https://github.com/user-attachments/assets/08082305-e965-4e03-b76b-725ecbda61bf" />


## Storage Design

- ### NAS-HDD is completely dedicated for SMB share managed through Fileserver. (refer to [storage](/storage))

<img width="1008" height="334" alt="image" src="https://github.com/user-attachments/assets/14ee230a-1a38-4243-b5db-60d0c96b33fa" />


<img width="945" height="213" alt="image" src="https://github.com/user-attachments/assets/b2394619-f4bd-428c-82ad-6cd47b0b05a3" />

- ### The Samba service exposes the storage through the `NAS` network share.

<img width="1651" height="732" alt="image" src="https://github.com/user-attachments/assets/aaf68b0f-02ce-4a1b-ac24-4ad9fbcab96c" />




## Access Management

The NAS provides shared storage infrastructure across the homelab.

Access is governed through role-based access control (RBAC), ensuring that storage access is managed according to authorized users and their assigned permissions.

## Network Security

Network segmentation, firewall policies and access restrictions are documented separately under the network security section of this repository.
