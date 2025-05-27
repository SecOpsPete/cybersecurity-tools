# 🔐 SSH Key Authentication Lab (Azure or VirtualBox)

A hands-on lab that walks you through setting up secure SSH access using public-private key pairs. You’ll SSH from one machine into an Ubuntu server using a secure RSA key pair — either with **Azure Cloud VMs** or **VirtualBox local VMs**.

---

## 🎯 Learning Objectives

- Generate and manage SSH key pairs
- Configure Ubuntu to accept key-based login
- Secure SSH by disabling password authentication
- Troubleshoot common SSH key issues

---

## 🛠️ Lab Environment Options

| Option       | Description                                      |
|--------------|--------------------------------------------------|
| ☁️ Azure      | SSH from Windows into an Ubuntu VM with public IP |
| 🖥️ VirtualBox | SSH from one local Ubuntu/Kali VM into another   |

---

## ⚙️ VirtualBox Network Setup

If using VirtualBox, ensure both VMs are on the same **Internal Network** or **Host-Only Adapter**:

1. Open VirtualBox > VM Settings > Network
2. Adapter 1 > Attached to: `Internal Network` or `Host-Only Adapter`
3. Boot both VMs and run `ip a` to find the server's private IP

---

## 🧪 Step-by-Step Lab Guide

### ✅ STEP 1: Generate SSH Key Pair (on Client Machine)

#### If you're on Windows (for Azure):
```powershell
ssh-keygen -t rsa -b 4096 -C "azureuser@ssh-lab"
```

#### If you're on a local Linux VM (for VirtualBox):
```bash
ssh-keygen -t rsa -b 4096 -C "client@local-vm"
```

- Accept the default location (`~/.ssh/id_rsa`)
- Enter a passphrase (optional)

---

### ✅ STEP 2: Copy Public Key to Server

#### ☁️ **Azure**
If your Azure VM was created with SSH key login, you're already set.

If not, connect with password and run:
```powershell
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh azureuser@<Azure_Public_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

#### 🖥️ **VirtualBox**
From your client VM, run:
```bash
ssh-copy-id username@<VM2_Private_IP>
```
Or manually:
```bash
cat ~/.ssh/id_rsa.pub | ssh username@<VM2_Private_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

### ✅ STEP 3: SSH into the Server

#### ☁️ **Azure**
```powershell
ssh azureuser@<Azure_Public_IP>
```

#### 🖥️ **VirtualBox**
```bash
ssh username@<VM2_Private_IP>
```

---

### ✅ STEP 4: Harden SSH Access (Optional but Recommended)

1. SSH into the server
2. Edit the SSH config:
```bash
sudo nano /etc/ssh/sshd_config
```

3. Update or confirm:
```conf
PasswordAuthentication no
PermitRootLogin no
```

4. Restart the SSH service:
```bash
sudo systemctl restart ssh
```

⚠️ If using Azure: Make sure your key works *before* doing this, or you could get locked out.

---

## 🧰 Permissions Checklist (on the Server)

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## 🧠 Troubleshooting Tips

| Symptom                          | Fix                                                                 |
|----------------------------------|----------------------------------------------------------------------|
| `Permission denied (publickey)` | Check if public key is in `~/.ssh/authorized_keys`                  |
| Key file ignored                 | Use the correct private key path (`~/.ssh/id_rsa`)                  |
| Wrong permissions                | `.ssh = 700`, `authorized_keys = 600`                              |
| Still asked for password        | Make sure you're using the private key, not `.pub`                 |
| Verbose output                  | Add `-v` to debug: `ssh -v user@host`                              |

---

## 📂 Suggested Folder Structure

```
ssh-key-authentication-lab/
├── README.md
├── scripts/
│   └── cleanup-ssh-keys.sh
├── screenshots/
│   └── ssh-keygen-example.png
```

---

## ✅ Summary

Whether you're using Azure or VirtualBox, you now have hands-on experience creating and using SSH key pairs for secure remote access. SSH keys are a security best practice and a foundational skill for cloud, DevOps, and cybersecurity professionals.

> 🔐 **SSH key authentication improves both security and convenience for server access.**

---

## 🔗 Resources

- [Azure SSH Access Docs](https://learn.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows)
- [VirtualBox Networking Modes](https://www.virtualbox.org/manual/ch06.html)
- [SSH Key Concepts (SSH.com)](https://www.ssh.com/academy/ssh/keygen)
