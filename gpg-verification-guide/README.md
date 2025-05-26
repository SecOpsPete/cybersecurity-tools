# 🔐 GPG Signature Verification Guide

This guide walks through the complete process of verifying the authenticity and integrity of a downloaded file using GPG, with a real-world example based on the Tor Browser installer.

---

## 📁 Files Used

- `tor-browser-windows-x86_64-portable-14.5.2.exe` — the file to verify  
- `tor-browser-windows-x86_64-portable-14.5.2.exe.asc` — the detached GPG signature

---

## 🧰 Prerequisites

- GPG installed (e.g., via [Gpg4win](https://gpg4win.org/))
- Both the `.exe` and `.asc` files saved in the same folder (e.g., Downloads)

---

## ⚙️ Step-by-Step Instructions (PowerShell)

```powershell
# STEP 1: Navigate to the Downloads folder
cd "$env:USERPROFILE\Downloads"

# STEP 2: Confirm both files are present
ls *.exe, *.asc

# STEP 3: Optional - check for hidden extensions like ".asc.txt"
ls *.asc*

# STEP 4: Import the Tor Browser Developers public GPG key
gpg --keyserver hkps://keyserver.ubuntu.com --recv-keys 0x78A65729

# STEP 5: View the full key fingerprint
gpg --fingerprint 0x78A65729

# STEP 6: (Manually verify) Ensure the fingerprint matches the official source
# Official Tor Project fingerprint:
# EF6E 286D DA85 EA2A 4BA7  DE68 4E2C 6E87 9329 8290

# STEP 7: Run the signature verification command
gpg --verify "tor-browser-windows-x86_64-portable-14.5.2.exe.asc" "tor-browser-windows-x86_64-portable-14.5.2.exe"

# STEP 8: Interpret the output
# - "Good signature from ..." = ✅ Success
# - "WARNING: This key is not certified..." = ⚠️ Informational only
# - "BAD signature" = ❌ Do NOT trust the file
