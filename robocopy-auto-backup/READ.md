# 📂 Automated File Backup with RoboCopy — `robocopy_backup_auto.bat`

A compact **Windows batch** backup that copies selected OneDrive folders to a secondary drive.  
**Not a mirror** — it copies new/changed files and preserves structure/metadata without deleting extras.  
Designed to run manually or via **Task Scheduler**, with every run appended to a central log.

---

## 🔧 Script

    @echo off
    setlocal

    REM SOURCE (OneDrive on C:) and DEST (backup on D:)
    set "SRC=C:<path>
    set "DST=D:<path>

    REM Logging
    mkdir "D:\BackupLogs" >nul 2>&1
    set "LOG=D:\BackupLogs\robocopy_backup.log"   <!-- example path -->

    REM ===== MAIN: call sync for each folder =====
    call :sync "file1"
    call :sync "file2"
    call :sync "file3"
    call :sync "file4"

    echo Done.
    endlocal
    exit /b

    REM ===== SUBROUTINE =====
    :sync
    robocopy "%SRC%\%~1" "%DST%\%~1" ^
      /E /FFT /R:1 /W:2 /NFL /NDL /TEE /DCOPY:DA /COPY:DAT /NP /LOG+:%LOG%

---

## ✅ What this does

- Copies only the folders you list from OneDrive into `D:<path>`.
- Preserves directory layout, file attributes, and timestamps.
- Skips already-matching files (fast re-runs).
- Appends output to `D:\BackupLogs\robocopy_backup.log`. <!-- example path -->

> Want true mirroring (including **deletions** of items missing at the source)? Add `/MIR` — intentionally **not used** here.

---

## ⚙️ Options (quick reference)

    /E            Copy all subdirectories (including empty)
    /FFT          FAT-style timestamp tolerance (reduces false mismatches)
    /R:1 /W:2     One retry, 2-second wait (prevents long hangs on locked files)
    /NFL /NDL     Suppress per-file and per-directory listings (cleaner output)
    /TEE          Write to console and log simultaneously
    /DCOPY:DA     Preserve directory data + attributes
    /COPY:DAT     Copy file Data, Attributes, Timestamps
    /NP           No progress percentages (smaller logs)
    /LOG+:<path>  Append to the central log file

---

## ▶️ How to use 
1. Save the file. 
2. Edit the folder list under the **MAIN** section as needed.  
3. Open **Command Prompt** and test:

       C:\> C:<path.bat>

   Confirm new lines appear in `D:\BackupLogs\robocopy_backup.log`. <!-- example path -->

---

## ⏰ Task Scheduler setup (optional)

- **Program:** `cmd.exe`  
- **Arguments:** `/c "C:\Scripts\robocopy_backup_auto.bat"`  <!-- example path -->
- **Run with highest privileges:** ✅  
- **Trigger:** As desired (daily, at logon, etc.)  
- **User:** SYSTEM or your admin account

---

## 🔍 Verifying success

- **Task result:** Last Run Result `0x0` and History shows “Action completed.”  
- **Logs:** New entries appended to `D:\BackupLogs\robocopy_backup.log`.  <!-- example path -->
- **Files:** Updated content present in `D:\<FolderName>\…`

---

## 🧠 Notes & tips

- Keep the backup drive label consistent (e.g., **Backup_A**) to avoid path mix-ups.  
- If you see “Access denied” or long stalls, re-run after closing apps that may lock files.  
- For larger jobs, consider adding `/MT:8` (multi-threaded copy) — optional and safe.

**Author:** [SecOpsPete](https://github.com/SecOpsPete) • **Last Updated:** September 2025
