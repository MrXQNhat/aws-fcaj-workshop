---
title: "Cloudflare Zero Trust Access"
date: 2026-03-01
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

### Step 1: Create Self-Hosted Access Application
1. Navigate to **Cloudflare Zero Trust** > **Access** > **Applications** > **Add an application**.
2. Select **Self-hosted**.
3. **Application name:** `SecureLearn Admin Portal`
4. **Application domain:** `admin.yourdomain.com`
5. **Session Duration:** Select `24 hours` and click **Next**.

### Step 2: Configure Admin Access Policy
1. **Policy name:** `Admin Access`
2. **Action:** Select **Allow**.
3. **Configure rules:**
   * **Selector:** Choose **Emails** (or **Emails ending in** for organizational domains).
   * **Value:** Enter authorized administrator email addresses.
4. Click **Next** and **Add application** to enforce Zero Trust perimeter authentication before anyone can reach the instructor/admin upload portal.
