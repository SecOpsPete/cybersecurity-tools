# 🛡️ Windows Threat Audit & Cleanup Automation Scripts

This project includes PowerShell scripts that enhance security visibility and hygiene on a Windows system. These tools support a proactive cybersecurity posture by surfacing common persistence mechanisms, startup anomalies, and system clutter.

---

## 📁 Repository Structure

```
C:\Scripts\
├── Threat-Audit.ps1                # Performs a system audit with security focus
├── Register-ThreatAuditTask.ps1    # Registers the audit script as a scheduled task
├── Clean-TempFolder.ps1            # (Optional) Manual temp cleanup utility
└── Logs\                           # Stores timestamped audit logs
```

---

## 🔍 Threat-Audit.ps1

### 🎯 Purpose

Performs a comprehensive local system threat audit covering:

- PowerShell Execution Policy and Logging
- Registry-based autoruns (HKCU + HKLM)
- Non-Microsoft scheduled tasks
- Auto-start services
- Open TCP connections
- Temporary folder contents

---

### 💻 Script

```powershell
<#
.SYNOPSIS
Performs a system threat audit and saves results to a timestamped log file.

.DESCRIPTION
Checks PowerShell execution policies, logging status, autoruns, scheduled tasks, services, TCP connections, and temp folder usage.

.USAGE
Run manually in an elevated PowerShell session.

.NOTES
Author: SecOpsPete
Date: July 2025
#>

# Create log folder if it doesn't exist
$logFolder = "C:\Scripts\Logs"
if (!(Test-Path -Path $logFolder)) {
    New-Item -ItemType Directory -Path $logFolder -Force | Out-Null
}

# Create timestamped log file path
$timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
$logPath = Join-Path $logFolder "ThreatAudit_$timestamp.txt"

# Start transcript
Start-Transcript -Path $logPath -Force

Write-Host "`n🔍 Starting Threat Audit...`n"

# [1] PowerShell Execution Policy
Write-Host "`n[1] PowerShell Execution Policy:`n"
Get-ExecutionPolicy -List

# [2] ScriptBlock Logging Status
Write-Host "`n[2] PowerShell ScriptBlock Logging:"
$regPath = "HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging"
if (Test-Path $regPath) {
    Get-ItemProperty -Path $regPath | Format-List
} else {
    Write-Host "Not enabled."
}

# [3] Autoruns: Current User and Local Machine
Write-Host "`n[3] Startup Registry Entries (Current User and Local Machine):`n"
Get-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"
Get-ItemProperty "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run"

# [4] Scheduled Tasks (Non-Microsoft)
Write-Host "`n[4] Non-Microsoft Scheduled Tasks:`n"
Get-ScheduledTask | Where-Object { $_.TaskName -notmatch "Microsoft" } | Format-Table TaskName, TaskPath, State -AutoSize

# [5] Auto-start Services
Write-Host "`n[5] Auto-Start Services:`n"
Get-CimInstance -ClassName Win32_Service | Where-Object { $_.StartMode -eq 'Auto' } | 
    Select-Object Name, DisplayName, StartMode, State, PathName | 
    Sort-Object -Property Name | 
    Format-Table -AutoSize

# [6] Active TCP Connections
Write-Host "`n[6] Established TCP Connections:`n"
Get-NetTCPConnection | Where-Object { $_.State -eq 'Established' } |
    Select-Object LocalAddress, RemoteAddress, RemotePort, State, OwningProcess |
    Format-Table -AutoSize

# [7] Temp Folder Size
Write-Host "`n[7] Temp Folder Status:"
$temp = Get-ChildItem -Path $env:TEMP -Recurse -ErrorAction SilentlyContinue
$size = ($temp | Measure-Object -Property Length -Sum).Sum / 1MB
"{0}Files: {1}, Total Size: {2:N2} MB" -f "`n", $temp.Count, $size | Write-Host

# End transcript
Stop-Transcript

```

---

## ⏰ Register-ThreatAuditTask.ps1

### 🎯 Purpose

Registers the `Threat-Audit.ps1` script to run every Monday at **8:05 AM**, with “Run task as soon as possible after a scheduled start is missed” enabled.

---

### 💻 Script

```powershell
<#
.SYNOPSIS
Registers a scheduled task to run Threat-Audit.ps1 weekly.

.DESCRIPTION
Creates a scheduled task named 'ThreatAuditWeekly' that runs every Monday at 8:05 AM.
If the system is off, it runs at next startup.

.AUTHOR
SecOpsPete
#>

$taskName = "ThreatAuditWeekly"
$scriptPath = "C:\Scripts\Threat-Audit.ps1"
$time = "08:05AM"
$days = "Monday"

$action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-NoProfile -ExecutionPolicy Bypass -File `"$scriptPath`""
$trigger = New-ScheduledTaskTrigger -Weekly -DaysOfWeek $days -At $time
$trigger.StartBoundary = ([datetime]::Now).Date.AddHours(8).AddMinutes(5).ToString("s")
$trigger.Enabled = $true
$triggerExecution = $trigger | Set-ScheduledTaskTrigger -RunOnStartupIfMissed $true

Register-ScheduledTask -Action $action -Trigger $trigger -TaskName $taskName -Description "Run weekly threat audit every $days at $time" -User "SYSTEM" -RunLevel Highest -Force

# Output
Get-ScheduledTask -TaskName $taskName | Select-Object TaskPath, TaskName, State
Write-Host "✅ Scheduled task '$taskName' registered to run every $days at $time (will run on next startup if missed)." -ForegroundColor Green
```

---

## 📝 Logging Behavior

The `Threat-Audit.ps1` script handles all logging internally. It creates a **timestamped log file** for every run in the `Logs` subfolder, using the format:

```
ThreatAudit_YYYY-MM-DD_HH-MM-SS.txt
```

You do **not** need to modify the scheduled task with `*>` output redirection — logging is already built into the script using `Start-Transcript`.

📂 Example:
```
C:\Scripts\Logs\
├── ThreatAudit_2025-07-15_23-32-47.txt
├── ThreatAudit_2025-07-16_00-04-59.txt
```

This preserves the output of every audit for historical reference and review.

---

## 🧹 Clean-TempFolder.ps1 (use with caution or perform manually)

### 🎯 Purpose

Deletes all files and subfolders inside the current user's temp directory. Use cautiously in production.

---

### 💻 Script

```powershell
<#
.SYNOPSIS
Deletes all files and folders from the user TEMP directory.

.DESCRIPTION
This is a hygiene cleanup script to reduce clutter and possible persistence hiding spots.

.AUTHOR
SecOpsPete
#>

Write-Host "🧹 Cleaning Temp Folder: $env:TEMP" -ForegroundColor Cyan
Get-ChildItem "$env:TEMP" -Recurse -Force -ErrorAction SilentlyContinue | Remove-Item -Force -Recurse -ErrorAction SilentlyContinue
Write-Host "✅ Temp folder cleaned." -ForegroundColor Green
```

---

## 💡 Cybersecurity Rationale

- **Startup entries**, scheduled tasks, and services are **common persistence vectors**.
- **Execution policy and logging** are essential for catching script-based attacks.
- **Temp folders** are used by malware and LOLBins to hide payloads.
- Frequent audits increase situational awareness and reduce dwell time.

---

## ✅ Usage Tips

- Place all `.ps1` files inside `C:\Scripts\`
- Logs are written to `C:\Scripts\Logs\`
- Run the task registration script once to automate weekly audits
- Pair with Sysmon, Defender, and your ELK/SIEM stack for maximum visibility

---

🧠 Created for defenders, students, and sysadmins looking to bring transparency to their Windows environment.
