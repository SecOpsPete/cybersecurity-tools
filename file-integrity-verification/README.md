# 🧪 File Integrity Verification Using SHA256 Hash (Wireshark Example)

This guide walks through how to verify the **integrity** of a downloaded file using a SHA256 checksum. Verifying file integrity ensures the file hasn’t been tampered with or corrupted during download — a critical part of secure software practices.

---

## 📦 Example Files

- `Wireshark-win64-4.0.10.exe` — the file to verify  
- `Wireshark-win64-4.0.10.exe.sha256` — the official file containing the published hashes from the [Wireshark download page](https://www.wireshark.org/download.html)

---

## 🧰 Prerequisites

- The downloaded `.exe` file  
- The corresponding `.sha256` file from the official source  
- PowerShell (preinstalled on Windows)

---

## ⚙️ Step-by-Step Instructions (PowerShell)

*(PowerShell script removed. Insert your own script or commands here.)*

---

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
