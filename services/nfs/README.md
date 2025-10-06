# 📂 NFS Server Setup

This is how I set up an NFS server on my **laptop** to share a media directory on my spare 1 TB HDD with my **desktop** (Jellyfin host).  
The setup ensures **centralized storage**, proper **user mapping**, and secure **NFSv4.2 mounts**.

---

## 🖴 Prepare the HDD (Laptop)
**Identify the HDD partition**:
`lsblk -f` <br>
Example  <br> `sda  931.5G` <br>
`└─sda1   ext4   linux-hdd   UUID=<my-hdd-UUID>` <br>
**Create mount point and mount the disk:** <br>
`sudo mkdir -p /mnt/hdd` <br>
`sudo mount /dev/sda1 /mnt/hdd` <br>
**Make the mount persistent:** <br>
Edit **/etc/fstab** by adding <br>
`UUID=<my-hdd-UUID>  /mnt/hdd  ext4  defaults  0 2` <br>
**Create export directories (the directory nfs server shares):** <br>
`sudo mkdir -p /mnt/hdd/exports/media`

---

## 1- Install NFS Server (Laptop)
`sudo apt update && sudo apt install -y nfs-kernel-server`

## 2- Create mediauser user and group (Laptop)
#### Create group with fixed GID
`sudo groupadd -g 1002 mediauser`

#### Create user with fixed UID and disable login
`sudo useradd -M -s /usr/sbin/nologin -u 1002 -g 1002 mediauser`

## 3- Set Ownership and Permissions (Laptop)
`sudo chown -R mediauser:mediauser /mnt/hdd/exports/media` <br>
`sudo chmod -R 775 /mnt/hdd/exports/media`

## 4- Configure NFS Exports (Laptop)
`sudo vim /etc/exports` <br>
`/mnt/hdd/exports/media  Desktop_IP(rw,sync,no_subtree_check,all_squash,anonuid=1002,anongid=1002,sec=sys)`

| Option             | Meaning                                                    |
| ------------------ | ---------------------------------------------------------- |
| `rw`               | Desktop can read/write files                               |
| `sync`             | Writes are committed immediately (safe for media)          |
| `no_subtree_check` | Improves performance for subfolders                        |
| `all_squash`       | Maps all desktop users to the anonymous user (`mediauser`) |
| `anonuid=1001`     | UID to map clients to (`mediauser`)                        |
| `anongid=1001`     | GID to map clients to (`mediauser`)                        |
| `sec=sys`          | Standard UNIX authentication                               |

Local Desktop IP (NFS client) : 192.168.0.50

## 5- Apply and Restart NFS (Laptop)
`sudo exportfs -ra` <br>
`sudo systemctl restart nfs-kernel-server`

## 6- allow NFS through UFW (laptop)
`sudo ufw allow from Desktop_IP to any port nfs`

Local Desktop IP (NFS client) : 192.168.0.50

## 7- Mount the Share (Desktop)
#### Create mount point
`sudo mkdir -p /mnt/media`

#### Mount NFS share
`sudo mount -t nfs -o vers=4.2 LAPTOP_IP:/mnt/hdd/exports/media /mnt/media`

Local Laptop IP (NFS server) : 192.168.0.60

## 8- Make Mount Persistent (Desktop)
`LAPTOP_IP:/mnt/hdd/exports/media  /mnt/media  nfs4  rw,hard,intr,vers=4.2,sec=sys  0 0`

Local Laptop IP (NFS server) : 192.168.0.60

