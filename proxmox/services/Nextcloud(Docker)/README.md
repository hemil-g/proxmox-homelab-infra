# Nextcloud – Self-Hosted Private Cloud

## Overview

Deployed Nextcloud as a self-hosted private cloud platform within my homelab, providing centralized file storage, synchronization and personal data management.

The deployment runs on a dedicated Ubuntu Server virtual machine hosted on Proxmox VE. Application components are containerized using Docker Compose, with Dockge providing a web-based interface for managing container stacks.

The architecture separates application services, database, caching and background processing into dedicated containers.

## Architecture

The deployment follows a multi-layer architecture:

1. **Proxmox VE:** Hosts the Ubuntu Server virtual machine.
2. **Ubuntu Server:** Provides the operating system and Docker environment.
3. **Docker Compose:** Defines and deploys the Nextcloud application stack.
4. **Dockge:** Provides centralized web-based management of Docker Compose stacks.
5. **Persistent Storage:** Maintains application data independently of container lifecycles.
6. **FortiGate Firewall:** Provides external connectivity through configured port forwarding.

![Nextcloud infrastructure architecture](./assets/architecture.png)

## Deployment Configuration

| Component | Configuration |
|---|---|
| Hypervisor | Proxmox VE |
| Operating System | Ubuntu Server |
| Container Platform | Docker |
| Orchestration | Docker Compose |
| Application | Nextcloud Apache |
| Database | PostgreSQL |
| Caching | Redis |
| Background Jobs | Nextcloud Cron |
| Application Port | 8080 → 80 |
| Stack Directory | `/opt/docker-data/docker-stacks/nextcloud` |

### Container Architecture

The Nextcloud deployment consists of four primary services:

- **Nextcloud:** Apache-based application container providing the web interface and cloud functionality.
- **PostgreSQL:** Dedicated relational database for application metadata and configuration.
- **Redis:** Caching and transactional file-locking support.
- **Cron:** Dedicated background-job container for scheduled Nextcloud maintenance tasks.

![Nextcloud Docker containers](./assets/docker-containers.png)

## Docker Stack Management – Dockge

Dockge is deployed as a Docker Compose management interface on the Ubuntu Server VM.

It provides a centralized web interface for managing Docker Compose stacks, allowing services to be viewed, started, stopped, updated and monitored without relying exclusively on command-line operations.

The management interface simplifies day-to-day administration while retaining Docker Compose configuration as the foundation of the deployment.

**Dockge deployment directory:** `/opt/docker-data/dockage`

**Stack directory:** `/opt/docker-stacks`

![Dockge dashboard showing Docker Compose stacks](./assets/dockge-dashboard.png)

## Storage Architecture

Storage is provisioned separately from the application containers to maintain persistent data across container lifecycle operations.

The deployment uses dedicated physical storage allocation for the virtual machine and Nextcloud data:

- **External HDD:** Used for virtual machine storage.
- **NAS-HDD:** The SMB-share configured using [Turnkey-FS](https://github.com/hemil-g/proxmox-infra/blob/main/proxmox/services/Turnkey-Fileserver(LXC).md) is used by Ubuntu server and then exposed to Nextcloud container to access user data from anywhere.

This separation distinguishes virtual machine infrastructure storage from user-generated application data.

![Proxmox virtual machine storage configuration](./assets/storage-configuration.png)

## Remote Accessibility

Nextcloud is accessible remotely through a FortiDDNS hostname using FortiGate port forwarding.

**External endpoint:**

The deployment uses direct firewall port forwarding witht a dedicated reverse-proxy deployment using nginx reverse proxy manager.

Nextcloud's external hostname and protocol settings are configured to support access through the external endpoint.

![Nextcloud remote access architecture](./assets/remote-access-flow.png)

## Deployment Management

The deployment is maintained using Docker Compose, with Dockge providing a graphical management interface.

This approach combines declarative service configuration with convenient operational management.

## Key Infrastructure Concepts

- Proxmox virtualization
- Ubuntu Server administration
- Docker and Docker Compose
- Dockge stack management
- Multi-container application architecture
- PostgreSQL database integration
- Redis caching and file locking
- Persistent storage management
- Scheduled background processing
- Remote service accessibility through FortiGate
