# 🛡️ Microsoft Defender ASR Rules for Hardening Windows

This project documents the use of **Attack Surface Reduction (ASR)** rules in Microsoft Defender Antivirus to reduce the attack surface of a Windows system. These tools support a proactive cybersecurity posture by blocking high-risk behaviors — especially those used in macro-based malware and Living-Off-the-Land (LOLBins) attacks.

---

## 📁 Repository Structure

```
C:\Scripts\
├── Enable-ASRMacroBlock.ps1         # Enables the Office macro ASR rule using pwsh.exe
└── Logs\                            # (Optional) Folder for logging outputs (not required)
```

---

## 🔍 Enable-ASRMacroBlock.ps1

### 🎯 Purpose

Applies the following ASR rule to harden Microsoft Defender:

| Rule Description                                 | GUID                                     | Status      |
|--------------------------------------------------|------------------------------------------|-------------|
| Block Office applications from creating child processes | `3B576869-A4EC-4529-8536-B80A7769E899` | ✅ Enabled |

This rule blocks Microsoft Office programs (like Word and Excel) from launching potentially malicious child processes like PowerShell, cmd.exe, or wscript.exe — a common malware tactic in phishing attacks.

---

### 💻 Script

```powershell
<#
.SYNOPSIS
Enables the ASR rule that prevents Office apps from spawning child processes.

.DESCRIPTION
This rule mitigates macro-based malware by blocking Office apps from launching other executables (e.g., PowerShell, cmd). Must be run with administrative rights while Microsoft Defender Antivirus is active.

.USAGE
Run in an elevated PowerShell 7+ session using pwsh.exe.

.NOTES
Author: SecOpsPete
Date: July 2025
#>

Add-MpPreference -AttackSurfaceReductionRules_Ids 3B576869-A4EC-4529-8536-B80A7769E899 `
                 -AttackSurfaceReductionRules_Actions Enabled

Write-Host "✅ ASR rule applied: Office child-process blocking enabled." -ForegroundColor Green
```

---

## 🔍 Verification

To confirm the ASR rule is active:

```powershell
Get-MpPreference | Select-Object AttackSurfaceReductionRules_Ids, AttackSurfaceReductionRules_Actions
```

Expected output:

```
AttackSurfaceReductionRules_Ids        : {3B576869-A4EC-4529-8536-B80A7769E899}
AttackSurfaceReductionRules_Actions    : {1}
```

Where:
- `1 = Enabled`
- `2 = Audit`
- `0 = Disabled`

---

## 🪪 Defender Must Be Active

ASR rules are only enforced when Microsoft Defender is the active antivirus engine. Confirm Defender is active with:

```powershell
Get-MpComputerStatus | Select-Object AMServiceEnabled, AntivirusEnabled, RealTimeProtectionEnabled
```

Expected result:

```
AMServiceEnabled           : True
AntivirusEnabled           : True
RealTimeProtectionEnabled  : True
```

---

## 💡 Cybersecurity Rationale

- 🛑 Blocks Office macro payloads from launching LOLBins like PowerShell or WScript.
- 🧰 Prevents initial execution stages of phishing and ransomware attacks.
- 🚫 Breaks common malware kill chains before they escalate.
- 🧼 Ideal low-friction hardening control for personal and enterprise environments alike.

---

## ✅ Future Plans

Stay Tuned! Additional ASR rules wil be included later for:

- Blocking credential theft via LSASS
- Preventing scripts from launching executables
- Ransomware behavior mitigation

---

## 🧩 References

- [Microsoft Docs: Attack Surface Reduction Rules](https://learn.microsoft.com/microsoft-365/security/defender-endpoint/attack-surface-reduction)
- [Microsoft Defender PowerShell Module](https://learn.microsoft.com/powershell/module/defender/)
- [Living-Off-the-Land Binaries (LOLBAS)](https://lolbas-project.github.io/)

---

**Author:** [SecOpsPete](https://github.com/SecOpsPete)  
**Last Updated:** July 2025
```
