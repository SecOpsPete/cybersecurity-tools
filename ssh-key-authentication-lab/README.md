# 🔐 SSH Key Authentication Lab (Azure or VirtualBox)

A hands-on lab that walks you through setting up secure SSH access using public-private key pairs. You’ll SSH from your personal PC (or a local VM) into an Ubuntu server hosted either on Azure or in VirtualBox, using a secure RSA key pair.

---

## 🎯 Learning Objectives

- Generate and manage SSH key pairs
- Configure Ubuntu to accept key-based login
- Secure SSH by disabling password authentication
- Troubleshoot common SSH key issues

---

## 🛠️ Lab Setup

Choose one of the following deployment options for your Ubuntu server target:

| Option       | Description                                      |
|--------------|--------------------------------------------------|
| ☁️ Azure      | SSH from your personal machine into an Ubuntu VM with a public IP |
| 🖥️ VirtualBox | SSH from one local VM (client) into another Ubuntu VM (server)    |

---

## ⚙️ VirtualBox Network Setup (If Using VirtualBox)

To enable communication between your two VirtualBox VMs:

1. Open VirtualBox → Select VM → Settings → Network  
2. Set Adapter 1 to: `Internal Network` or `Host-Only Adapter`  
3. Use the **same network name** for both VMs  
4. Boot both VMs and run `ip a` to view their IP addresses  

---

### 🔍 Connectivity Test (VirtualBox Only)

#### Step A: Check IPs

On both VMs:

```bash
ip a
```

Find the `inet` line under `eth0` or `enp0s3`.

#### Step B: Ping Test

From the **client VM**, ping the server:

```bash
ping <Target_VM_IP>
```

Success output looks like:

```
64 bytes from 10.0.2.15: icmp_seq=1 ttl=64 time=0.456 ms
```

If it fails with `Destination Host Unreachable`, ensure:
- Both VMs are on the same VirtualBox network
- You check updated IPs after boot
- UFW isn’t blocking SSH or ICMP:
  ```bash
  sudo ufw allow ssh
  sudo ufw enable
  ```

---

## ☁️ Azure VM Setup

If you are using Azure, follow this process to deploy your VM properly for SSH key access.

### Azure Step 1: Verify or Create an SSH Key (on Client PC)

In PowerShell:

```powershell
cat $env:USERPROFILE\.ssh\id_rsa.pub
```

If no key exists, generate one:

```powershell
ssh-keygen -t rsa -b 4096 -C "peter@azurelab"
```

---

### Azure Step 2: Deploy an Ubuntu VM with Your SSH Key

1. Go to [portal.azure.com](https://portal.azure.com)  
2. Create a resource → Select **Ubuntu Server**

3. Under "Administrator account":
   - **Authentication type**: `SSH public key`
   - **Username**: e.g., `azureuser` → this will be your SSH login name

4. Under "SSH Public Key":
   - Choose **Use existing public key**
   - Paste the output from:
     ```powershell
     cat $env:USERPROFILE\.ssh\id_rsa.pub
     ```
   - ⚠️ Avoid extra whitespace or broken formatting

5. Allow inbound port:
   - **Port 22 (SSH)**

6. Click **Review + Create** and then **Create**  
7. After deployment, note your **public IP address**

---

## ⚠️ Warning: Do Not Overwrite Existing SSH Key Files

When generating a new SSH key pair with `ssh-keygen`, you may see a prompt like this:

```bash
Enter file in which to save the key (/home/youruser/.ssh/id_rsa):
```

If you press Enter and a key already exists at that location, you'll be asked:

```bash
/home/youruser/.ssh/id_rsa already exists.
Overwrite (y/n)?
```

❗ **Do not choose "yes" unless you intend to replace the existing key.**

Overwriting your key will break access to any servers (such as Azure VMs) that were configured with the original key. This will result in errors like:

```bash
Permission denied (publickey)
```

✅ Instead, either reuse your existing key or generate a new one with a different filename:

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa_new
```

This ensures existing connections remain valid.

---

## 🧪 SSH Authentication Lab (Step-by-Step Guide)

These are the main steps regardless of your environment (Azure or VirtualBox).

---

### ✅ STEP 1: Generate SSH Key Pair (on Client)

#### Windows (PowerShell)

```powershell
ssh-keygen -t rsa -b 4096 -C "azureuser@ssh-lab"
```

#### Linux (local VM)

```bash
ssh-keygen -t rsa -b 4096 -C "client@local-vm"
```

- Press Enter to accept default save path  
- Set a passphrase (optional)

---

### ✅ STEP 2: Copy Your Public Key to the Target Server

#### ☁️ Azure (if key wasn’t already added)

```powershell
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh azureuser@<Azure_Public_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

#### 🖥️ VirtualBox

Use `ssh-copy-id` if available:

```bash
ssh-copy-id username@<Target_VM_IP>
```

Or manually:

```bash
cat ~/.ssh/id_rsa.pub | ssh username@<Target_VM_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

### ✅ STEP 3: Connect to the Server via SSH

#### Azure Example:

```powershell
ssh azureuser@<Azure_Public_IP>
```

#### VirtualBox Example:

```bash
ssh username@<Target_VM_IP>
```

If you used a passphrase when generating the key, you’ll be prompted to enter it (not your system password).

---

### ✅ STEP 4: Harden SSH Configuration (Optional but Recommended)

Once connected to the Ubuntu server:

1. Edit the SSH daemon config:

```bash
sudo nano /etc/ssh/sshd_config
```

2. Set the following:

```conf
PasswordAuthentication no
PermitRootLogin no
```

3. Save and exit Nano:
- Ctrl + O → Enter → Ctrl + X

4. Restart the SSH service:

```bash
sudo systemctl restart ssh
```

5. Optionally verify:

```bash
sudo systemctl status ssh
```

⚠️ **If using Azure**, confirm key login works before doing this, or you could get locked out.

---

## 🔐 Permissions Checklist

On the target server, make sure your `.ssh` directory and key file have the correct permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## 🧠 Troubleshooting Guide

| Symptom                          | Suggested Fix                                                       |
|----------------------------------|----------------------------------------------------------------------|
| `Permission denied (publickey)` | Ensure key is in `~/.ssh/authorized_keys` and matches your private key |
| Still asked for password         | You may be using the `.pub` key instead of the private one           |
| Key ignored                      | File permissions or incorrect path                                  |
| Debug login attempts             | Use verbose output: `ssh -v username@host`                          |

---

## 📁 Suggested Project Folder Structure

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

Whether you're using Azure or VirtualBox, this lab has walked you through securely accessing a remote Linux machine using SSH key authentication. This method is a foundational best practice for cybersecurity, DevOps, and system administration professionals.

> 🔐 **SSH key authentication improves both security and convenience over traditional passwords.**

---

## 🔗 Resources

- [Azure SSH Access Docs](https://learn.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows)
- [VirtualBox Networking Modes](https://www.virtualbox.org/manual/ch06.html)
- [SSH Key Concepts (SSH.com)](https://www.ssh.com/academy/ssh/keygen)
