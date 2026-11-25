
# TurnKey File Server LXC
#Those who dont know [Turnkey linux](https://www.turnkeylinux.org/) is  library of pre-packaged, ready-to-deploy software templates to run them as Linux containers(LXC)

## Overview

Deployed TurnKey File Server as a centralized network storage service within my Proxmox infrastructure.

The service provides shared storage over SMB/CIFS, allowing infrastructure components and authorized systems to access centralized data through a common network share.

## Architecture

TurnKey File Server runs as an LXC container on Proxmox VE.

Storage is provisioned and managed at the Proxmox level, then exposed to the container through an LXC mount point:

- **Container:** TurnKey File Server (CT 103)
- **Storage mount point:** `/mnt/smb-share`
- **File sharing protocol:** SMB/CIFS
- **Share name:** `NAS`
- **Access management:** Role-based access control (RBAC)

## Storage Design

A Proxmox-managed storage volume is mapped into the LXC container using a mount point. This allows the file server to provide network-accessible storage without requiring the storage volume to be independently managed inside the container.

The Samba service exposes the storage through the `NAS` network share.

## Access Management

The NAS provides shared storage infrastructure across the homelab.

Access is governed through role-based access control (RBAC), ensuring that storage access is managed according to authorized users and their assigned permissions.

## Network Security

Network segmentation, firewall policies and access restrictions are documented separately under the network security section of this repository.
