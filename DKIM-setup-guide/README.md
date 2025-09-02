# 🔐 Microsoft 365 DKIM Setup & Verification (petervanrossum.com)

This guide walks through publishing the **two DKIM CNAME records** for Microsoft 365, enabling DKIM in **Defender / Exchange Admin Center**, and verifying with **message headers** and **DMARC aggregate reports**.

> ✅ In mid-2025, I experienced a phishing attempt where an attacker spoofed my own domain to send malicious emails. This revealed a critical gap in my domain’s email security posture: despite GoDaddy advertising “advanced email protections,” DKIM (DomainKeys Identified Mail) had not been automatically provisioned for my domain. Without DKIM, recipients had no cryptographic assurance that messages claiming to come from my domain were authentic, leaving the domain vulnerable to spoofing.

To remediate this I manually added the required two CNAME records (selector1 and selector2) at my DNS host, enabled DKIM signing in Microsoft 365, and verified the configuration. Gmail headers confirmed dkim=pass (selector1), and subsequent DMARC aggregate reports reflected pass results — providing clear evidence that DKIM was successfully deployed and aligned. This strengthened the domain’s overall email security by ensuring authenticity, improving deliverability, and reducing the risk of phishing from spoofed addresses.

---

## 🧰 Prerequisites

- Verified domain (e.g., `petervanrossum.com`) in your Microsoft 365 tenant  
- Admin access to **Microsoft 365 Defender** or **Exchange Admin Center**  
- Control of DNS records at your registrar/host (GoDaddy, Cloudflare, etc.)  

---

## ⚡ Quick Path (TL;DR)

1. Copy the two CNAMEs from M365 DKIM settings  
2. Publish them at your DNS provider (**CNAME**, not TXT)  
3. Wait for propagation → Enable DKIM  
4. Send test to Gmail → **Show original** → confirm `dkim=pass`  
5. Confirm DMARC reports show **pass**  

---

## 🛠️ Why DKIM Matters

- 🛡️ **SPF** → Who is allowed to send mail  
- 🔏 **DKIM** → Signed messages proving authenticity  
- 📊 **DMARC** → Aligns SPF/DKIM with From: and enforces policy  

With DKIM enabled, you confirmed Gmail showed `dkim=pass` and DMARC aligned.

---

## 📝 Step 1 — Get the Two CNAMEs

From **Defender → Threat policies → DKIM** (or EAC → Mail flow → DKIM):

**Hosts (publish at DNS):**

    selector1._domainkey.petervanrossum.com
    selector2._domainkey.petervanrossum.com

**Targets (copy from portal, example shape):**

    selector1-<unique>._domainkey.<yourtenant>.onmicrosoft.com
    selector2-<unique>._domainkey.<yourtenant>.onmicrosoft.com

> 🔑 The `<unique>` portion is tenant-specific. Copy/paste exactly — no edits.

---

## 🌐 Step 2 — Publish the CNAMEs

| Name (Host)                               | Type  | Value (Target)                                               | TTL   |
|-------------------------------------------|-------|---------------------------------------------------------------|-------|
| `selector1._domainkey.petervanrossum.com` | CNAME | `selector1-<unique>._domainkey.<yourtenant>.onmicrosoft.com` | 3600s |
| `selector2._domainkey.petervanrossum.com` | CNAME | `selector2-<unique>._domainkey.<yourtenant>.onmicrosoft.com` | 3600s |

💡 **Tips**  
- Some DNS UIs want only the host part (`selector1._domainkey`)  
- Use **CNAME**, not TXT  
- Avoid stray whitespace  

---

## ⏳ Step 3 — Wait & Verify DNS

**Windows (PowerShell / CMD):**

    nslookup -type=cname selector1._domainkey.petervanrossum.com
    nslookup -type=cname selector2._domainkey.petervanrossum.com

**Linux/macOS:**

    dig +short CNAME selector1._domainkey.petervanrossum.com
    dig +short CNAME selector2._domainkey.petervanrossum.com

Expected: resolution to the `onmicrosoft.com` targets.

---

## ✅ Step 4 — Enable DKIM

- Return to DKIM settings for your domain  
- Click **Enable** (only active once both CNAMEs resolve)  
- Outbound mail is now signed  

> 🎉 You enabled on **Aug 31, 2025**

---

## 🔍 Step 5 — Verify with Gmail

Send a test to Gmail → **Show original**. Example:

    Authentication-Results: mx.google.com;
      dkim=pass header.i=@petervanrossum.com header.s=selector1 header.b=...
      spf=pass ...
      dmarc=pass (p=quarantine sp=quarantine) header.from=petervanrossum.com

Expected: `dkim=pass`, `header.s=selector1`, DMARC aligned.

---

## 📊 Step 6 — Confirm in DMARC Reports

Publish a DMARC record with RUA for reports:

    _dmarc.petervanrossum.com. 3600 IN TXT "v=DMARC1; p=quarantine; rua=mailto:dmarc-reports@petervanrossum.com; ruf=mailto:dmarc-forensics@petervanrossum.com; fo=1"

I saw **pass** after enablement.  
👉 Consider moving from `p=quarantine` → `p=reject` when stable.

---

## 🔄 Key Rotation

Microsoft provides two selectors to support seamless rotation:

1. Keep **both** CNAMEs in DNS  
2. Microsoft may switch active selector automatically  
3. Rotation happens with no mail breakage  

---

## 🛠️ Troubleshooting

- ⚪ **Enable button grayed out** → DNS not propagated, wrong record type, or typo  
- 🔴 **dkim=neutral/fail** → Not enabled in portal, wrong relay, or bad DNS  
- 🟡 **DMARC fail** → Check From: alignment, SPF coverage, wait for reports  
- 🔵 **ARC confusion** → ARC ≠ DKIM/DMARC, focus on `dkim=pass`  

---


## 🗓️ Change Log

- **2025-08-31** — Added `selector1`/`selector2` CNAMEs, enabled DKIM, verified Gmail `dkim=pass`, DMARC pass.  
- **2025-09-02** — Published complete README guide.  

---

## 👤 Credits

Implementation & verification: **Peter Van Rossum**  
Notes compiled from practical enablement in **Microsoft 365 Defender** with real header & DMARC evidence.

