# 🔐 GPG Signature Verification Guide

This guide walks through the complete process of verifying the authenticity and integrity of a downloaded file using GPG, with a real-world example based on the Tor Browser installer.

---

## 📁 Files Used

- `tor-browser-windows-x86_64-portable-14.5.2.exe` — the file to verify  
- `tor-browser-windows-x86_64-portable-14.5.2.exe.asc` — the detached GPG signature

---

### 🧰 Prerequisites

- GPG installed (e.g., via [Gpg4win](https://gpg4win.org/))
- Both the `.exe` and `.asc` files saved in the same folder (e.g., Downloads)

---

## ⚙️ Step-by-Step Instructions (PowerShell)

**STEP 1: Navigate to the Downloads folder**

```powershell
cd "$env:USERPROFILE\Downloads"
```

**STEP 2: Confirm both files are present**

```powershell
ls *.exe, *.asc
```

**STEP 3: Optional - check for hidden extensions like ".asc.txt"**

```powershell
ls *.asc*
```

**STEP 4: Import the Tor Browser Developers public GPG key**

```powershell
gpg --keyserver hkps://keyserver.ubuntu.com --recv-keys 0x78A65729
```

**STEP 5: View the full key fingerprint**

```powershell
gpg --fingerprint 0x78A65729
```

**STEP 6: (Optional - Manually verify) Ensure the fingerprint matches the official source**

Official Tor Project fingerprint:  

`EF6E 286D DA85 EA2A 4BA7  DE68 4E2C 6E87 9329 8290`


**STEP 7: Run the signature verification command**

```powershell
gpg --verify "tor-browser-windows-x86_64-portable-14.5.2.exe.asc" "tor-browser-windows-x86_64-portable-14.5.2.exe"
```

**STEP 8: Interpret the output**

- `"Good signature from ..."` = ✅ **Success**
- `"WARNING: This key is not certified..."` = ⚠️ **Informational only**
- `"BAD signature"` = ❌ **Do NOT trust the file**

---

## 🧠 Why This Matters

Verifying downloaded files protects against:

- 🧪 **Tampering or corruption** — ensures the file wasn’t altered during download
- 🧨 **Malware from unofficial mirrors** — prevents installing malicious lookalikes
- 🛠️ **Supply-chain attacks** — confirms the file really came from the vendor

This is a fundamental part of secure software practices and a critical skill in cybersecurity.

---

