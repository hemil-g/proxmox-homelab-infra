# Storage Architecture

The storage infrastructure uses internal and external HDD storage, alongside SSD-based storage for virtual machine workloads.

## Storage allocation

| Storage | Purpose
|:---|:---|
| Internal HDD | Nextcloud data storage
| External HDD | Virtual machine storage
| SSD storage | Virtual machine operating system and boot storage

The storage configuration is designed to separate application data from virtual machine storage and improve workload organization.
