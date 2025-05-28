# 🔐 SSH Key Authentication Lab (Azure or VirtualBox)

A hands-on lab that walks you through setting up secure SSH access using public-private key pairs. You’ll SSH from your personal PC (or a local VM) into an Ubuntu server hosted either on Azure or in VirtualBox, using a secure RSA key pair.

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

### 🧪 Connectivity Check (VirtualBox Only)

Before beginning the SSH key configuration, ensure your two VirtualBox VMs can communicate over the network.

#### ✅ Step 1: Confirm IP Addresses

On **each VM**, run:

```bash
ip a
```

Look for the IP address under the active adapter (e.g., `enp0s3`, `eth0`).

#### ✅ Step 2: Test Connectivity

From the **client VM**, ping the server:

```bash
ping <Target_VM_IP>
```

If it works, you’ll see:  
`64 bytes from 10.0.2.15: icmp_seq=1 ttl=64 time=0.456 ms`

If it fails (e.g., "Destination Host Unreachable"), troubleshoot the network setup.

---

### 🛠️ If Pinging Fails

- Ensure both VMs are on the **same VirtualBox network**
- Use `ip a` again after any network changes to confirm new IPs
- On the server VM, allow SSH and ping via UFW:
  ```bash
  sudo ufw allow ssh
  sudo ufw enable
  ```

Once successful, proceed to SSH key configuration.

---

## ☁️ Azure Setup Steps

Before you deploy your VM, make sure you have an SSH public key ready. If you haven’t created one yet, follow [✅ STEP 1: Generate SSH Key Pair](#-step-1-generate-ssh-key-pair-on-client-machine) below.

Then follow the instructions below to deploy your Ubuntu VM in Azure.


---

### ✅ Step 2: Deploy Azure VM with Public Key

When creating a new Ubuntu VM in the Azure portal, follow these steps to configure it for SSH key authentication. This ensures your public key is authorized during provisioning, allowing you to log in securely without a password.

1. **Go to Azure Portal**:  
   Navigate to [portal.azure.com](https://portal.azure.com) and click **Create a resource** > **Ubuntu Server**.

2. **Authentication Type**:  
   On the **Basics** tab, under "Administrator account," select:  
   **☑️ SSH public key** — This method is more secure than using a password.

3. **Username**:  
   Choose a username (e.g., `azureuser`).  
   ⚠️ You’ll use this exact name to SSH into the VM later:
   ```bash
   ssh azureuser@<Azure_Public_IP>
   ```

4. **SSH Public Key Source**:  
   Choose:  
   **☑️ Use existing public key**

5. **Paste Your Public Key**:  
   Copy the entire output of the following command on your personal PC:
   ```powershell
   cat $env:USERPROFILE\.ssh\id_rsa.pub
   ```
   Paste this into the **SSH public key** field.  
   🔐 Make sure you don’t accidentally include extra text or line breaks.

6. **Allow Inbound Port 22**:  
   Under **Inbound port rules**, allow **SSH (22)** access so you can connect remotely.

7. **Complete Provisioning**:  
   Click **Review + Create**, then **Create**.  
   When the VM is finished deploying, note its **public IP address** — you’ll use this to SSH into it.

---


---

## 🧪 Step-by-Step Lab Guide

### ✅ STEP 1: Generate SSH Key Pair (on Client Machine)

#### On Windows (Azure):

```powershell
ssh-keygen -t rsa -b 4096 -C "azureuser@ssh-lab"
```

#### On Linux (VirtualBox):

```bash
ssh-keygen -t rsa -b 4096 -C "client@local-vm"
```

Accept defaults (`~/.ssh/id_rsa`). Optionally enter a passphrase.

---

### ✅ STEP 2: Copy Public Key to Server

#### ☁️ Azure
If the key was added during deployment, skip this.

Otherwise, copy it manually:

```powershell
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh azureuser@<Azure_Public_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

#### 🖥️ VirtualBox
Use the correct username for the target VM:

```bash
ssh-copy-id username@<VM2_Private_IP>
```

Or manually:

```bash
cat ~/.ssh/id_rsa.pub | ssh username@<VM2_Private_IP> "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

### ✅ STEP 3: SSH into the Server

#### ☁️ Azure

```powershell
ssh azureuser@<Azure_Public_IP>
```

#### 🖥️ VirtualBox

```bash
ssh username@<VM2_Private_IP>
```

If you added a passphrase to the key, enter it when prompted.

---

### ✅ STEP 4: Harden SSH Access (Optional but Recommended)

1. SSH into the server  
2. Edit the SSH config:

```bash
sudo nano /etc/ssh/sshd_config
```

Update or add:

```conf
PasswordAuthentication no
PermitRootLogin no
```

Save and exit Nano:
- Ctrl + O → Enter → Ctrl + X

3. Restart SSH:

```bash
sudo systemctl restart ssh
```

4. Test SSH login again from a new terminal window:

```bash
ssh username@<Target_IP>
```

You should get in without a password prompt.

⚠️ **Azure warning**: Ensure SSH login works *before* disabling password auth — or you risk being locked out.

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
