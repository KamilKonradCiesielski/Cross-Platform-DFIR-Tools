# 📧 Thunderbird Email Acquisition & WP.pl Artifacts

## Overview
This section covers the forensic acquisition of email data from the Polish provider **WP.pl** by leveraging **Mozilla Thunderbird** as an intermediary collection tool on macOS. 

## 🛠️ Acquisition Toolkit
* **Source:** WP.pl Webmail (IMAP/SMTP)
* **Tool:** Mozilla Thunderbird (macOS build)
* **Methodology:** Full IMAP Synchronization + Profile Cold-Copy
* **Artifact Format:** MBOX (Berkeley Mailbox)

---

## 📁 Investigation Workflow

### I. Target Environment Preparation (WP.pl)
Before initiating acquisition, remote access protocols must be manually enabled in the provider's web interface:
1. Log in to WP.pl webmail and navigate to **Opcje** -> **IMAP / POP**.
2. Enable **IMAP - pobieraj i wysyłaj wiadomości**.
*Note: Failure to enable this will result in "Authentication Failed" errors during the Thunderbird handshake phase.*

### II. Local Storage Configuration
To ensure a forensic-grade acquisition where local storage contains full message bodies and attachments (not just headers):
1. **Right-click** on the WP.pl account in the Thunderbird sidebar and select **Settings** (Ustawienia).
2. Navigate to **Synchronization & Storage** (Synchronizacja i przechowywanie).
3. Ensure **"Keep messages in all folders for this account on this computer"** is **checked**.
4. Click **Advanced...** (Zaawansowane) and verify all relevant folders (Inbox, Sent, Trash, etc.) are selected for sync.

### III. Execution of Full Data Sync
1. Return to the main Thunderbird interface.
2. Navigate to **File** → **Offline** → **Download/Sync Now...**
3. In the pop-up window, check **"Mail messages"** and click **OK**.
4. Monitor the progress bar until the synchronization is complete.

---

### 🛡️ Data Validation (Post-Acquisition)
Verify the integrity of the local MBOX storage before proceeding to analysis:
1. **Enable Offline Mode:** Go to **File** → **Offline** → **Work Offline**.
2. **Body Content Verification:** Attempt to open several random messages (especially those with attachments).
3. **Success Criteria:** * If content is accessible offline, the **Full Acquisition** is successful.
    * If prompted for a connection or "Body not downloaded" appears, the forensic integrity is compromised.

---

## 🔍 Forensic Analysis

### 1. Locating Artifacts on macOS
The local evidence is stored within the user's Library. The default forensic path for Thunderbird on macOS is:
`~/Library/Thunderbird/Profiles/<profile_name>/ImapMail/imap.wp.pl/`

### 2. Forensic Footprint
* **Unified Logging System (ULS):** Thunderbird's network activity and file system writes can be captured using:
  `log show --process Thunderbird`
* **File Metadata:** Modification of `global-messages-db.sqlite` (SQLite database) occurs during indexing and can be used to timeline user activity.

## ⚠️ DFIR Best Practices
* **Integrity:** Always compute MD5/SHA-256 hashes of the `ImapMail` directory immediately after synchronization.
* **MIME Preservation:** While MBOX is standard, exporting specific messages to `.eml` via *ImportExportTools NG* is recommended for granular metadata analysis (e.g., X-Originating-IP, Message-ID).
* **Chain of Custody:** Document the transition from Webmail (Cloud) to Thunderbird (Local) as a critical step in the acquisition report.

