# 📂 NFS Server Setup

This guide documents how to configure an NFS server on the **laptop** to share media with the **desktop** (Jellyfin host).  
The setup ensures **centralized storage**, proper **user mapping**, and secure **NFSv4.2 mounts**.

---

## 1️⃣ Install NFS Server (Laptop)
```bash
sudo apt update && sudo apt install -y nfs-kernel-server

## 2️⃣ Create mediauser (Laptop)
# Create group with fixed GID
sudo groupadd -g 1001 mediauser

# Create user with fixed UID and disable login
sudo useradd -M -s /usr/sbin/nologin -u 1001 -g 1001 mediauser

## 3️⃣ Set Ownership and Permissions (Laptop)
sudo chown -R mediauser:mediauser /mnt/hdd/exports/media
sudo chmod -R 775 /mnt/hdd/exports/media

## 4️⃣ Configure NFS Exports (Laptop)
/mnt/hdd/exports/media  192.168.0.50(rw,sync,no_subtree_check,all_squash,anonuid=1001,anongid=1001,sec=sys)

| Option             | Meaning                                                    |
| ------------------ | ---------------------------------------------------------- |
| `rw`               | Desktop can read/write files                               |
| `sync`             | Writes are committed immediately (safe for media)          |
| `no_subtree_check` | Improves performance for subfolders                        |
| `all_squash`       | Maps all desktop users to the anonymous user (`mediauser`) |
| `anonuid=1001`     | UID to map clients to (`mediauser`)                        |
| `anongid=1001`     | GID to map clients to (`mediauser`)                        |
| `sec=sys`          | Standard UNIX authentication                               |

Laptop (NFS server) IP: 192.168.0.60

Desktop (NFS client) IP: 192.168.0.50

## 5️⃣ Apply and Restart NFS (Laptop)
sudo exportfs -ra
sudo systemctl restart nfs-kernel-server

## 6️⃣ Mount the Share (Desktop)
# Create mount point
sudo mkdir -p /mnt/media

# Mount NFS share
sudo mount -t nfs -o vers=4.2 192.168.0.60:/mnt/hdd/exports/media /mnt/media

## 7️⃣ Make Mount Persistent (Desktop)
192.168.0.60:/mnt/hdd/exports/media  /mnt/media  nfs4  rw,hard,intr,vers=4.2,sec=sys  0 0

