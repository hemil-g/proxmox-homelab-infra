# Nextcloud Deployment Guide

## 01. Infrastructure Preparation

### 1.1 Virtual Machine Provisioning

Nextcloud is hosted on a dedicated Ubuntu Server virtual machine running on Proxmox VE.

The VM provides an isolated environment for running the Docker-based application stack.

**Deployment considerations:**
- Dedicated VM for application workloads.
- Separate storage allocation for application data.
- Docker-based service deployment.
- Independent lifecycle management from other Proxmox workloads.

### 1.2 Operating System Verification

 Installation and allocated resources.

'nightcrawler@ubuntu:/$ free -h
               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       1.7Gi       120Mi       116Mi       1.9Gi       1.6Gi
Swap:          2.9Gi       657Mi       2.3Gi
nightcrawler@ubuntu:/$ df -h
Filesystem           Size  Used Avail Use% Mounted on
tmpfs                680M  5.9M  674M   1% /run
/dev/sda2             35G   29G  3.5G  90% /
tmpfs                1.7G  2.8M  1.7G   1% /dev/shm
none                 1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
none                 1.0M     0  1.0M   0% /run/credentials/systemd-resolved.service
tmpfs                1.7G   12K  1.7G   1% /tmp
**//192.168.10.11/NAS  458G  155G  303G  34% /mnt/smb**-------------------> Turnkey Fileserver SMB attached to Ubuntu
tmpfs                340M   72K  340M   1% /run/user/60580
tmpfs                340M   92K  340M   1% /run/user/1000'




## 02. Docker Environment

### 2.1 Docker Installation and Verification

Docker provides the container runtime used to deploy and manage the Nextcloud application stack.

Verify the installation:

```bash
docker --version
docker compose version
docker info
```

### 2.2 Docker Directory Structure

The deployment uses organized directories to separate Docker management files and application stacks.

```text
/opt/
├── docker-data/
│   ├── dockage/
│   └── docker-stacks/
│       └── nextcloud/
└── stacks/
```

## 03. Dockge Deployment and Management

Dockge provides a graphical interface for managing Docker Compose stacks.

It simplifies routine operations such as starting, stopping, updating and inspecting deployed services while retaining Compose files as the source of configuration.

**Deployment directory:**

```bash
/opt/docker-data/dockage
```

**Stack directory:**

```bash
/opt/stacks
```

Verify the Dockge container:

```bash
docker ps
docker logs dockge
```

![Dockge dashboard](./assets/05-dockge-dashboard.png)

## 04. Nextcloud Application Stack

The Nextcloud deployment consists of four dedicated services:

- Nextcloud Apache
- PostgreSQL
- Redis
- Nextcloud Cron

The services are defined using Docker Compose, allowing the application stack to be deployed and maintained as a coordinated unit.

**Stack location:**

```bash
/opt/docker-data/docker-stacks/nextcloud
```

Inspect the Compose configuration:

```bash
docker compose config
docker compose ps
```

![Nextcloud Compose configuration](./assets/06-compose-stack.png)

## 05. Application Verification

Verify that the application containers are operational:

```bash
docker ps
docker compose logs --tail=100
```

Check the individual service logs when troubleshooting:

```bash
docker compose logs nextcloud
docker compose logs postgres
docker compose logs redis
docker compose logs cron
```

![Running Docker containers](./assets/07-container-status.png)

## 06. Remote Accessibility

Nextcloud is exposed through the configured FortiGate port-forwarding arrangement and FortiDDNS hostname.

External endpoint:

```text
https://gigi.fortiddns.com:10301
```

![Nextcloud application dashboard](./assets/08-nextcloud-dashboard.png)

Network security policies and firewall implementation are documented separately in the homelab network security documentation.
