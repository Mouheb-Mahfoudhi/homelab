# 🔐 SSH Key-Based Authentication Setup

This is how I set up key-only SSH access between my desktop (client) and laptop (server) to allow secure connections within my homelab. <br>
(The same process was repeated in reverse to allow SSH access in both directions.)

The setup ensures:

- 🔒 Encrypted communication

- 🚫 No password-based logins

- 🧩 Modern Ed25519 key algorithm

---

## ⚙️ 1- Generate SSH Keys (Client)
```
ssh-keygen -t ed25519 -C "desktop-to-laptop"
```
by default this will create:

- ~/.ssh/id_ed25519 → private key

- ~/.ssh/id_ed25519.pub → public key

## 📤 2- Copy Public Key to the server (Client)
```
ssh-copy-id $Laptop_User@$Laptop_IP
```
This copies the public key generated earlier in ~/.ssh/authorized_keys (on the server side)

Laptop's Local  IP : 192.168.0.60
## 🖥 3- Configure SSH Daemon (Server)

```
sudo vim /etc/ssh/sshd_config
```
Uncomment and change these lines <br>

```
PubkeyAuthentication yes
PasswordAuthentication no
```
Then restart SSH:
```
sudo systemctl restart ssh
```

## 🗝️ 4- Use the SSH Agent (client)
```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```
This loads the private key into the agent, so now SSH sessions won’t require retyping passphrases.

## 🌐 5- (Optional) Define a Host Alias (client)
Create or edit ~/.ssh/config on the client side:
```
Host laptop-server
    HostName $Laptop_IP
    User $Laptop_User
    IdentityFile ~/.ssh/id_ed25519
```
Laptop's Local IP : 192.168.0.60

Now I can simply connect to my laptop using : `ssh laptop-server`

## 🔁 6- Repeat for Reverse Access
Repeat the same steps from laptop (client) → desktop (server)
to allow secure, key-only SSH access both ways across the homelab.
