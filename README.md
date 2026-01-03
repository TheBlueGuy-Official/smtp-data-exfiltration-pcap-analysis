# SMTP Cleartext Credential Exposure & Malicious Attachment Analysis (Zeek + PCAP)

## Overview
This repository documents the forensic analysis of a network capture containing **unencrypted SMTP traffic**.  
Using **Zeek**, **Wireshark**, and manual protocol analysis, the investigation reconstructs:

- Cleartext SMTP authentication credentials
- Email metadata (sender, recipient, subject)
- A DOCX attachment transmitted via SMTP
- Host attribution (IP, MAC, hostname, OS hints)

The case demonstrates how lack of TLS on SMTP submission leads to **full credential and content disclosure**.

---

## Objectives
- Identify SMTP senders and recipients
- Detect cleartext (non‑TLS) email sessions
- Extract email content and attachments
- Recover Base64‑encoded credentials
- Correlate Zeek logs (`smtp.log`, `files.log`, `dhcp.log`)
- Reconstruct and safely analyze a DOCX attachment

---

## Tools Used
- **Zeek** (Network Security Monitor)
- **Wireshark / tshark**
- **tcpdump**
- **base64**
- **awk / sed**
- **unzip**
- **olevba**
- **docx2txt**

---
>>>>>>> c845565 (README.md)
