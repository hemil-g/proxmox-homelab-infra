# Objective: Isolate different workloads using VLAN-based network segmentation

### Physical switch configured to carry multiple VLANs with VLAN trunk connected to Proxmox server
<img width="1456" height="352" alt="trunk" src="https://github.com/user-attachments/assets/8d2fab27-cf8e-45d1-b3c6-88f7472d7cf3" />


<img width="1492" height="616" alt="vlans" src="https://github.com/user-attachments/assets/ba0af729-2f6c-4a3c-8a37-2aab6cd3e36a" />


### Proxmox connected to switch through a VLAN-aware virtual bridge
<img width="1387" height="510" alt="prxmox vlans" src="https://github.com/user-attachments/assets/62fe4681-401d-47a4-9d0b-8422c0c77cf2" />


### Individual VM/LXC interfaces assigned to appropriate VLANs. #Not shown due to security reasons
### Inter-VLAN traffic controlled by the firewall policiesand role based access(RBAC). #Not shown due to security reasons

