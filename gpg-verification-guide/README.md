# STEP 1: Open PowerShell and navigate to your Downloads folder
cd "$env:USERPROFILE\Downloads"

# STEP 2: Confirm that both the .exe and .asc files are present
ls *.exe, *.asc

# STEP 3: (Optional but recommended) Inspect the exact filename of the .asc file
#         This helps catch issues like hidden extensions (.asc.txt)
ls *.asc*

# STEP 4: Import the developer's public GPG key from a trusted keyserver
gpg --keyserver hkps://keyserver.ubuntu.com --recv-keys 0x78A65729

# STEP 5: Check the full fingerprint of the imported key
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
