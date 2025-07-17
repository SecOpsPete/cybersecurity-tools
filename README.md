# 🛡️ Cybersecurity Tools

A curated collection of practical scripts, guides, and verification tools to support secure operations, threat mitigation, and responsible software handling. Each item is built with real-world application in mind and tailored for cybersecurity professionals and learners alike.

---

## 📂 Included Tools

- 🛡️ [Threat Audit & Cleanup Automation](./win-threat-audit-cleanup-automation/README.md)  
  Run a weekly PowerShell audit to surface autoruns, suspicious services, TCP connections, and missing logging; includes optional temp folder cleanup and scheduled task automation.

- 🛡️ [Defender ASR Hardening Scripts](./microsoft-defender-attack-surface-reduction/README.md)  
  Block macro-based malware by enabling Microsoft Defender’s Attack Surface Reduction rule to prevent Office apps from launching child processes like PowerShell or cmd.

- 🔍 [Process Investigation: Suspicious Windows Executables](./process-investigation-windows/README.md)  
  Triage and analyze processes with PowerShell: inspect metadata, validate signatures, and check hashes via VirusTotal.

- 🔐 [GPG Signature Verification Guide](https://github.com/SecOpsPete/cybersecurity-tools/tree/main/gpg-verification-guide)  
  Learn how to verify the authenticity of downloaded files using GPG digital signatures and official public keys.

- 🧪 [File Integrity Verification (SHA256)](https://github.com/SecOpsPete/cybersecurity-tools/tree/main/file-integrity-verification)  
  Ensure downloaded files haven’t been tampered with or corrupted by validating their SHA256 hash using PowerShell.

- 🔐 [SSH Key Authentication Lab](https://github.com/SecOpsPete/cybersecurity-tools/tree/main/ssh-key-authentication-lab)  
  Step-by-step guide to securely access Linux machines (Ubuntu Server here) using SSH public/private key pairs via Azure or VirtualBox.


---

## 🧠 Purpose

These tools are designed to promote hands-on security practices in areas like:

- Secure file validation  
- Incident response preparation  
- Forensic data integrity  
- Supply chain defense

New tools and topics will be added regularly as the project expands.

---

> ✍️ Maintained by [Peter Van Rossum](https://www.linkedin.com/in/vanr/), as part of his practical cybersecurity portfolio.

