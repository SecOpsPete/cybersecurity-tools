# 🧪 File Integrity Verification Using SHA256 Hash (Tor Browser Example)

This guide walks through how to verify the **integrity** of a downloaded file using a SHA256 checksum. Verifying file integrity ensures the file hasn’t been tampered with or corrupted during download — a critical part of secure software practices.

---

## 📦 Example Files

- `tor-browser-windows-x86_64-portable-14.5.2.exe` — the file to verify  
- `sha256sums.txt` — the official file containing the published hashes

---

## 🧰 Prerequisites

- The downloaded `.exe` file
- The corresponding `sha256sums.txt` file from the official source
- PowerShell (preinstalled on Windows)

---

## ⚙️ Step-by-Step Instructions (PowerShell)

```powershell
# STEP 1: Navigate to the folder containing your downloaded files
cd "$env:USERPROFILE\Downloads"

# STEP 2: Confirm the presence of the .exe and sha256sums.txt files
ls *.exe, *.txt

# STEP 3: View the official SHA256 hash from the text file
Get-Content .\sha256sums.txt

# STEP 4: Compute the SHA256 hash of the downloaded installer
$myHash = (Get-FileHash .\tor-browser-windows-x86_64-portable-14.5.2.exe -Algorithm SHA256).Hash

# STEP 5: (Optional) Manually assign the official hash for comparison
$officialHash = "the_official_sha256_hash_here"  # Replace with actual value from sha256sums.txt

# STEP 6: Compare your hash with the official hash
if ($myHash -eq $officialHash) {
    Write-Host "`n✅ Hashes match. File integrity verified." -ForegroundColor Green
} else {
    Write-Host "`n❌ Hash mismatch. File may be corrupted or tampered with." -ForegroundColor Red
}

## 🧠 Why File Integrity Verification Matters

Checking a file’s hash is a key step in any secure download process. It confirms the file was not altered, corrupted, or replaced before execution.

---

### 🔍 Key Benefits

- 🧪 **Detects corruption** — Ensures the file wasn’t damaged during download (e.g., incomplete or interrupted transfer)
- 🧨 **Prevents tampering** — Protects against malicious modifications from compromised mirrors or supply chain attacks
- 🛡️ **Confirms authenticity** — When no digital signature is available, a hash check still ensures the file is genuine

---

### 📌 Real-World Use Cases

- ✅ Verifying open-source tools from GitHub or vendor sites
- ✅ Validating incident response toolkits before execution
- ✅ Ensuring forensic evidence files remain unaltered during transfer

---

Hash verification is a foundational skill for cybersecurity professionals and an essential habit in secure software workflows.
