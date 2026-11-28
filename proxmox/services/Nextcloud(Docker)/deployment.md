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
 
```bash
nightcrawler@ubuntu:/$ free -h
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
tmpfs                340M   92K  340M   1% /run/user/1000
```

Attached SMB share to Ubuntu server: 

```bash
root@ubuntu:~# findmnt -t cifs 
TARGET SOURCE   FSTYPE OPTIONS
/mnt/smb
       //192.168.10.11/NAS
                cifs   rw,relatime,vers=3.0,cache=strict,upcall_target=app,username=****,uid=**,forceuid,gid=**,forcegid,addr=192.168.10.11,file_mode=****,dir_mode=*****,soft,nounix,mapposix,reparse=nfs,nativesocket,
root@ubuntu:~#
```

Configured `/etc/fstab`  to automatically reconnect to this share every time the system boots up.

```bash
root@ubuntu:~# cat /etc/fstab 
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/7d97355d-858b-4b70-9518-d2954815b143 / ext4 defaults 0 1
/swap.img	none	swap	sw	0	0
//192.168.10.11/NAS /mnt/smb cifs credentials=/root/nextcloud_cred,vers=3.0,uid=**,gid=**,file_mode=****,dir_mode=****,_netdev,nofail 0 0
root@ubuntu:~# 
root@ubuntu:~# ls
nextcloud_cred  snap
root@ubuntu:~# 
```

## 02. Docker Environment

### 2.1 Docker Installation and Verification

Docker provides the container runtime used to deploy and manage the Nextcloud application stack.

Verify the installation:

```bash
nightcrawler@ubuntu:/$ 
nightcrawler@ubuntu:/$ sudo docker ps
[sudo: authenticate] Password:         
CONTAINER ID   IMAGE                             COMMAND                  CREATED        STATUS                  PORTS                                                                                      NAMES
780136072e9d   nextcloud:apache                  "/entrypoint.sh apac…"   3 months ago   Up 2 months (healthy)   0.0.0.0:****->80/tcp, [::]:****->80/tcp                                                    nextcloud-nextcloud-1
52e2258b6733   jc21/nginx-proxy-manager:2.15.1   "/init"                  3 months ago   Up 2 months             0.0.0.0:**-**->80-81/tcp, [::]:**-**->80-81/tcp, 0.0.0.0:****->443/tcp, [::]:****->443/tcp   nginx-reverseproxy-npm-1
5b68270f7bea   nextcloud:apache                  "/cron.sh"               3 months ago   Up 2 months             **/tcp                                                                                     nextcloud-cron-1
5bf67ba5553c   postgres:18-alpine                "docker-entrypoint.s…"   3 months ago   Up 2 months (healthy)   ***/tcp                                                                                   nextcloud-db-1
75446dc9fe90   redis:8-alpine                    "docker-entrypoint.s…"   3 months ago   Up 2 months             ***/tcp                                                                                   nextcloud-redis-1
7611e6163e26   louislam/dockge:1                 "/usr/bin/dumb-init …"   3 months ago   Up 2 months (healthy)   0.0.0.0:****->****/tcp, [::]:****->****/tcp                                                dockage-dockge-1
nightcrawler@ubuntu:/$
nightcrawler@ubuntu:/$ docker --version
Docker version 29.1.3, build 29.1.3-0ubuntu4.1
nightcrawler@ubuntu:/$ docker compose version
Docker Compose version 2.40.3+ds1-0ubuntu1
nightcrawler@ubuntu:/$ 
```

### 2.2 Docker Directory Structure

The deployment uses organized directories to separate Docker management files and application stacks.

```text
/opt/
├── docker-data/
│   ├── dockage/
│   └── docker-stacks/
│       └── nextcloud/
└── conatinerd/
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
/opt/containerd
``
<img width="1699" height="678" alt="image" src="https://github.com/user-attachments/assets/dc3931b0-54be-4fe0-a186-fc3a11b6e419" />

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

<img width="1803" height="1030" alt="image" src="https://github.com/user-attachments/assets/b097ea33-c6c8-4b41-9763-86c92008ee60" />

Check [docker-compose.yml](https://github.com/hemil-g/proxmox-infra/blob/main/proxmox/services/Nextcloud(Docker)/docker-compose.yml) file for Nextcloud stack.

## 05. Application Verification

Verify that the application containers are operational
Check the individual service logs when troubleshooting:

```bash
root@ubuntu:/opt/docker-data/docker-stacks/nextcloud# docker compose logs nextcloud -n 5 -f
nextcloud-1  | 127.0.0.1 - - [28/Nov/2026:05:13:36 +0000] "GET /status.php HTTP/1.1" 200 1063 "-" "curl/8.14.1"
nextcloud-1  | 127.0.0.1 - - [28/Nov/2026:05:14:06 +0000] "GET /status.php HTTP/1.1" 200 1063 "-" "curl/8.14.1"
nextcloud-1  | 127.0.0.1 - - [28/Nov/2026:05:14:36 +0000] "GET /status.php HTTP/1.1" 200 1063 "-" "curl/8.14.1"
nextcloud-1  | 127.0.0.1 - - [28/Nov/2026:05:15:06 +0000] "GET /status.php HTTP/1.1" 200 1063 "-" "curl/8.14.1"
nextcloud-1  | 127.0.0.1 - - [28/Nov/2026:05:15:36 +0000] "GET /status.php HTTP/1.1" 200 1063 "-" "curl/8.14.1"
root@ubuntu:/opt/docker-data/docker-stacks/nextcloud# docker compose logs cron -n 5 -f
cron-1  | crond: USER **** pid 21523 cmd php -f /var/www/html/cron.php
cron-1  | crond: USER **** pid 21524 cmd php -f /var/www/html/cron.php
cron-1  | crond: USER **** pid 21525 cmd php -f /var/www/html/cron.php
cron-1  | crond: USER **** pid 21526 cmd php -f /var/www/html/cron.php
cron-1  | crond: USER **** pid 21527 cmd php -f /var/www/html/cron.php
^[[A^C
root@ubuntu:/opt/docker-data/docker-stacks/nextcloud# docker compose logs db -n 5 -f
db-1  | 2026-11-28 05:07:33.879 UTC [1623556] LOG:  checkpoint complete: wrote 29 buffers (0.2%), wrote 1 SLRU buffers; 0 WAL file(s) added, 0 removed, 0 recycled; write=2.916 s, sync=0.008 s, total=2.939 s; sync files=13, longest=0.007 s, average=0.001 s; distance=153 kB, estimate=258 kB; lsn=1/1593DA50, redo lsn=1/1593D9F8
db-1  | 2026-11-28 05:12:30.933 UTC [1623556] LOG:  checkpoint starting: time
db-1  | 2026-11-28 05:12:33.973 UTC [1623556] LOG:  checkpoint complete: wrote 30 buffers (0.2%), wrote 1 SLRU buffers; 0 WAL file(s) added, 0 removed, 0 recycled; write=3.022 s, sync=0.004 s, total=3.041 s; sync files=14, longest=0.003 s, average=0.001 s; distance=166 kB, estimate=249 kB; lsn=1/159674C8, redo lsn=1/15967438
db-1  | 2026-11-28 05:17:31.072 UTC [1623556] LOG:  checkpoint starting: time
db-1  | 2026-11-28 05:17:34.202 UTC [1623556] LOG:  checkpoint complete: wrote 31 buffers (0.2%), wrote 1 SLRU buffers; 0 WAL file(s) added, 0 removed, 0 recycled; write=3.110 s, sync=0.004 s, total=3.130 s; sync files=14, longest=0.003 s, average=0.001 s; distance=164 kB, estimate=240 kB; lsn=1/159904E0, redo lsn=1/15990488
^[[A^C
root@ubuntu:/opt/docker-data/docker-stacks/nextcloud# docker compose logs redis -n 5 -f
redis-1  | 1:M 28 Nov 2026 05:08:54.066 * Background saving started by pid 2172
redis-1  | 2172:C 28 Nov 2026 05:08:54.087 * BGSAVE done, 2 keys saved, 0 keys skipped, 221 bytes written.
redis-1  | 2172:C 28 Nov 2026 05:08:54.093 * DB saved on disk
redis-1  | 2172:C 28 Nov 2026 05:08:54.093 * Fork CoW for RDB: current 0 MB, peak 0 MB, average 0 MB
redis-1  | 1:M 28 Nov 2026 05:08:54.166 * Background saving terminated with success
^X^C
root@ubuntu:/opt/docker-data/docker-stacks/nextcloud# 
```

## 06. Remote Accessibility

Nextcloud is exposed through the configured FortiGate port-forwarding arrangement and DDNS hostname using NGINX Reverse-Proxy with LetsEncrypt certificate.

<img width="1891" height="706" alt="image" src="https://github.com/user-attachments/assets/af981d99-f476-4a84-9999-d0035830d3db" />

