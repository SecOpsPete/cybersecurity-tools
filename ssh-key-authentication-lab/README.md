# 🔐 SSH Key Authentication Lab (Azure Ubuntu Example)

A hands-on lab that walks you through setting up secure SSH access using public-private key pairs. In this real-world scenario, you’ll SSH from your **Windows 10+ machine** into an **Ubuntu Server hosted on Azure** using a secure RSA key pair.

---

## 🎯 Learning Objectives

- Generate and manage SSH key pairs on Windows
- Configure an Ubuntu server to accept key-based login
- Secure SSH by disabling password authentication
- Troubleshoot common SSH key issues

---

## 🛠️ Lab Environment

| Component        | Example                                   |
|------------------|-------------------------------------------|
| Local Host       | Windows 10+ (PowerShell / WSL / Git Bash) |
| Remote Host      | Ubuntu 22.04 LTS in Azure VM              |
| SSH Tool         | PowerShell or Git Bash (`ssh` command)    |

---

## 🧪 Step-by-Step Lab Guide

### ✅ STEP 1: Generate SSH Key Pair (on Windows)

Open **PowerShell** and run:

```powershell
ssh-keygen -t rsa -b 4096 -C "azureuser@ssh-lab"
```

- Press `Enter` to accept the default location:  
  `C:\Users\<YourUsername>\.ssh\id_rsa`
- Enter a **passphrase** (optional but recommended)

---

### ✅ STEP 2: Access Azure VM and Add Your Public Key

If your VM isn't created yet, launch one via the Azure Portal with:
- **SSH public key** as auth method
- Username: `azureuser`

If the VM is already created with password login:
1. Connect using password:
    ```powershell
    ssh azureuser@<VM_Public_IP>
    ```

2. On your Windows machine, copy your public key to the VM:

    ```powershell
    type $env:USERPROFILE\.ssh\id_rsa.pub | ssh azureuser@<VM_Public_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
    ```

---

### ✅ STEP 3: Log In Using Your SSH Key

Now test the key-based connection:

```powershell
ssh azureuser@<VM_Public_IP>
```

You should **no longer be prompted for a password** (unless you used a passphrase).

---

### ✅ STEP 4: (Optional) Harden SSH Access

To enforce key-based login only:

1. SSH into the VM
2. Edit the SSH daemon config:

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

3. Change or ensure these lines are present:

    ```conf
    PasswordAuthentication no
    PermitRootLogin no
    ```

4. Restart SSH service:

    ```bash
    sudo systemctl restart ssh
    ```

⚠️ **Caution**: If you lock yourself out, use the Azure serial console or reset your VM's SSH keys via the portal.

---

## 🧰 Permissions Checklist (on Ubuntu VM)

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## 🧠 Troubleshooting Tips

| Symptom                          | Fix                                                                 |
|----------------------------------|----------------------------------------------------------------------|
| `Permission denied (publickey)` | Check if public key is in `~/.ssh/authorized_keys`                  |
| Key file ignored on Windows     | Use correct file path: `C:\Users\<YourUsername>\.ssh\id_rsa`         |
| Wrong permissions                | Ensure `.ssh = 700` and `authorized_keys = 600` on remote host      |
| Still asked for password        | Double-check you’re using the private key and not the `.pub` file   |
| Use verbose mode                | Add `-v` flag for SSH debugging: `ssh -v azureuser@<VM_IP>`         |

---

## 📂 Folder Structure (if uploading scripts/screenshots)

```
ssh-key-authentication-lab/
├── README.md
├── scripts/
│   └── cleanup-ssh-keys.ps1
├── screenshots/
│   └── ssh-keygen-windows.png
```

---

## ✅ Summary

This lab walked through the real-world process of using SSH key pairs to secure access to an Ubuntu server in Azure. By replacing password authentication with cryptographic keys, you reduce attack surface and streamline login processes for automation and secure admin access.

> 🔐 **SSH key-based authentication is the industry standard for secure remote access.**

---

## 🔗 Additional Resources

- [Azure VM SSH Access Docs](https://learn.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows)
- [OpenSSH Key Management](https://www.ssh.com/academy/ssh/keygen)
