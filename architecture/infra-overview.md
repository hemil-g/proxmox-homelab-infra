# Homelab Infrastructure – Architecture Overview

## **1. Project Overview**

This project documents my personal homelab infrastructure, built to gain hands-on experience in network engineering, cybersecurity, virtualization, Linux administration, containerization, and self-hosted services.

The environment is built around Proxmox VE, with FortiGate providing network security and an Ubuntu virtual machine hosting Docker-based applications.

The primary objectives are:

Build and maintain a virtualized infrastructure.

Explore network security, firewall management, and VLAN segmentation.

Deploy and manage self-hosted applications.

Understand storage management and virtualization.

Develop practical Linux and infrastructure administration skills.

Expand into automation, cloud infrastructure, and DevOps.

## **2. Physical Network Architecture**

Network topology: Also refer to [Home-network](Home-network.jpg)

The physical network follows this topology:

                  Internet
                     |
                  ISP Router
                     |
                  Firewall
                     |
                 Network Switch
                     |
              802.1Q VLAN Trunk
                     |
                 Proxmox VE
                     |
              VLAN-Aware Bridge
                     |
          +----------+----------+
          |                     |
        VLAN A                VLAN C
          |                     |
     Containers                VMs

## **3. VLAN Configuration**

The connection between the physical network switch and Proxmox server operates as a VLAN trunk.

Multiple VLANs are carried across this connection, allowing different virtual machines and LXC containers to be assigned to their respective VLANs.

This provides logical network separation between workloads while using shared physical infrastructure.

The VLAN configuration and associated network policies will be documented separately.

## **7. Future Improvements**

The homelab will continue evolving as new technologies and infrastructure projects are introduced.

Planned improvements include:

Infrastructure monitoring and alerting.

Improved storage backup and recovery.

Infrastructure automation

CI/CD pipelines using GitHub Actions.

Cloud infrastructure experimentation with Azure and AWS.

Security hardening and vulnerability management.


