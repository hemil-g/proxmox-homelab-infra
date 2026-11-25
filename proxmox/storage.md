# Storage Architecture

The storage infrastructure uses internal and external HDD storage, alongside SSD-based storage for virtual machine workloads.

## Storage allocation

| Storage | Purpose
|:---|:---|
| NAS-HDD | 2 HDDs configured with Raid1. Setup as Directory with EXT4 for NAS data storage.
| Internal-ssd | 2 256GB SSDs in a volume group (VG) configured as 512GB LVM. Best for VM and container disks where speed matter. Use as primary OS storage volume for VMs.
| local,local-lvm | **local** disk use to store and manage ISO images, Docker images, backups and snapshots. **local-lvm** configured as lvm-thin for deployment requiring on-demand allocation 

The storage configuration is designed to separate application data from virtual machine storage and improve workload organization.

<img width="702" height="512" alt="image" src="https://github.com/user-attachments/assets/1828f374-a1ae-4aa6-886e-fb49a01f37c3" />

