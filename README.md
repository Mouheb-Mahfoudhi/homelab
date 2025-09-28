# Homelab

Personal homelab for cybersecurity, DevOps, and software engineering practice.  
Runs several VMs on a Linux Mint desktop host, with containerized services, automation scripts, and VPN access.  
A Linux Mint laptop runs an NFS server to provide centralized storage for the lab.

---

## 💻 System Specifications

### Desktop

| Component        | Specification                 |
|-----------------|-------------------------------|
| CPU             | AMD Ryzen 5 5600              |
| RAM             | 16 GB DDR4                     |
| GPU             | Nvidia RTX 3060               |
| Storage         | 1 TB SSD                       |
| OS              | Linux Mint 22.1 Cinnamon      |
| Local IP        | 192.168.0.50/24               |
| Virtualization  | KVM/QEMU + virt-manager       |

### Virtual Machines on Desktop

| VM   | OS                | vCPU | RAM |
|------|-----------------|-----|-----|
| VM1  | Kali Linux 6.12.38 | 2   | 4 GB |
| VM2  | Windows 11 24H2    | 4   | 8 GB |

### Laptop

| Component        | Specification                 |
|-----------------|-------------------------------|
| CPU             | AMD Ryzen 5 3550H              |
| RAM             | 16 GB DDR4                     |
| GPU             | AMD Radeon RX560X             |
| Storage         | 500 GB SSD + 1 TB HDD          |
| OS              | Linux Mint 22.1 Cinnamon      |
| Local IP        | 192.168.0.60/24               |
