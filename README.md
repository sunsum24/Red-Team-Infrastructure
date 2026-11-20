# 🥷 Red Team Infrastructure Design

<div align="left">
  <a href="https://attack.mitre.org/">
    <img src="https://img.shields.io/badge/Red_Team-Infrastructure-red?style=for-the-badge" alt="Red Team" />
  </a>
  <a href="https://attack.mitre.org/techniques/T1090/">
    <img src="https://img.shields.io/badge/MITRE_ATT%26CK-T1090-blue?style=for-the-badge" alt="MITRE T1090" />
  </a>
</div>

## 📌 Executive Summary
During a Red Team engagement, protecting the operator's identity and the Command & Control (C2) infrastructure is critical. If the Blue Team discovers the C2 server, the entire operation is burned.

This repository documents the architecture for a secure, resilient Red Team infrastructure. It utilizes **Redirectors**, **Domain Fronting**, and **Phishing infrastructure** to obscure the true C2 server from defenders and threat intelligence platforms.

## 🏗️ Infrastructure Architecture

<img width="1280" height="640" alt="operation flow" src="https://github.com/user-attachments/assets/79b2c0e9-f29d-4ba2-a1a0-c36faa6a4b52" />


### 1. The Attack Platform (Operator)
The operator uses a hardened Linux VM (e.g., Kali or Ubuntu) located behind a VPN or a dedicated VPS. No direct connection is ever made to the target from the operator's true IP address.

### 2. The Redirector (The Shield)
Instead of pointing the payload directly to the C2 server, it points to a **Redirector** (e.g., an Nginx reverse proxy or Apache mod_rewrite).
- **Function:** The redirector forwards legitimate-looking traffic (e.g., HTTP/S) to the C2 server, but blocks traffic from known security vendors (e.g., VirusTotal scanners, Mandiant IPs).
- **Benefit:** If the redirector is blacklisted, the operator simply spins up a new one without having to rebuild the entire C2 infrastructure.

### 3. Command & Control (C2) Server
The C2 server is hosted on a completely different VPS provider. It only accepts connections from the Redirector's IP address, ensuring it remains hidden from the target network.

---

## 🔄 Attack Chain (MITRE ATT&CK)
1. **Initial Access (T1566):** Phishing email sent via the SMTP server.
2. **Execution (T1204):** Target clicks the link and downloads the payload.
3. **Command and Control (T1090):** The payload beacons to the Redirector, which proxies traffic to the C2 Server.

---

## 🛡️ Defensive Evasion Techniques
- **IP Filtering:** The redirector is configured to block traffic from known security vendors (e.g., CrowdStrike, Palo Alto, VirusTotal).
- **Domain Categorization:** Domains are aged and categorized as "Business" or "Finance" to bypass firewall blocks.
- **HTTPS Encryption:** All traffic between the payload, redirector, and C2 is encrypted using valid SSL/TLS certificates to prevent deep packet inspection (DPI).

## ⚠️ Disclaimer
This documentation is for authorized Red Team engagements and educational purposes only. 
