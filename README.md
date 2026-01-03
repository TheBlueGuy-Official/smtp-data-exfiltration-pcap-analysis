# SMTP Cleartext Credential Exposure & Malicious Attachment Analysis (Zeek + PCAP)

## Overview
This repository documents the forensic analysis of a network capture containing **unencrypted SMTP traffic**.  
Using **Zeek**, **Wireshark**, and manual protocol analysis, the investigation reconstructs:

- Cleartext SMTP authentication credentials
- Email metadata (sender, recipient, subject)
- A DOCX attachment transmitted via SMTP
- Host attribution (IP, MAC, hostname, OS hints)

The pcap is provided in a password-protected archive.

Password: pc@p

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

## Incident Summary

- **Client IP:** `192.168.30.108`
- **Hostname:** `annlaptop`
- **MAC Address:** `00:21:70:4d:4f:ae` (Apple OUI)
- **SMTP Server:** `64.12.168.40:587` (AOL)
- **Email Client:** Microsoft Outlook Express 6.00.2900.2180
- **TLS Used:** ❌ No (cleartext)

### Emails Observed
| Subject | Recipient |
|------|---------|
| need a favor | inter0pt1c@aol.com |
| lunch next week | d4rktangent@gmail.com |
| rendezvous | mistersekritx@aol.com |

---

## Cleartext Credential Exposure

SMTP authentication occurred using `AUTH LOGIN` without TLS.

### Extracted Credentials (Base64 decoded)
| Type | Value |
|---|---|
| Username | `sneakyg33ky` |
| Password | `s00pers3kr1t` |

>  This represents a **high‑severity security issue**, as credentials were fully exposed on the network.

---

## Attachment Analysis

- **Filename:** `secretrendezvous.docx`
- **MIME Type:** `application/vnd.openxmlformats-officedocument.wordprocessingml.document`
- **File UID (Zeek):** `FgtEkt24g0F9SSREe`
- **Transport:** SMTP (cleartext)

The attachment was reconstructed by extracting Base64 content from the SMTP DATA stream and decoding it.

### Verification
```bash
file secretrendezvous.docx
```
---

## Zeek Analysis Workflow

#### Identify senders

```bash
Email Sender
┌──(root㉿kali)-[/home/kali]
└─# zeek-cut id.orig_h mailfrom < smtp.log | sort | uniq -c | sort -nr
      3 192.168.30.108  sneakyg33ky@aol.com
```
#### Identify recipients IP

```bash
┌──(root㉿kali)-[/home/kali]
└─# zeek-cut id.resp_h < smtp.log | sort | uniq -c | sort -nr

      3 64.12.168.40
```
#### Detect non‑TLS SMTP

```bash
┌──(root㉿kali)-[/home/kali]
└─# zeek-cut id.orig_h mailfrom rcptto tls < smtp.log | grep F

192.168.30.108  sneakyg33ky@aol.com     inter0pt1c@aol.com      F
192.168.30.108  sneakyg33ky@aol.com     d4rktangent@gmail.com   F
192.168.30.108  sneakyg33ky@aol.com     mistersekritx@aol.com   F
```
#### Correlate attachments

```bash
┌──(root㉿kali)-[/home/kali]
└─# zeek-cut ts id.orig_h mailfrom rcptto subject < smtp.log | column -t

1305660786.286393  192.168.30.108  sneakyg33ky@aol.com  inter0pt1c@aol.com     need        a     favor
1305660855.561152  192.168.30.108  sneakyg33ky@aol.com  d4rktangent@gmail.com  lunch       next  week
1305660916.021405  192.168.30.108  sneakyg33ky@aol.com  mistersekritx@aol.com  rendezvous 
```

---

## MITRE ATT&CK Mapping

| Technique | ID |
|---------|----|
| Phishing: Attachment | T1566.001 | 
| Exfiltration Over Unencrypted Channel | T1041 | 
| Valid Accounts (credential exposure) | T1078 |

---

## Disclaimer

This repository is for educational and defensive security research only.
All data is analyzed in a controlled lab environment.


---

⭐ If you find this analysis useful, consider starring the repository.

