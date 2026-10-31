# Network Forensics & Malware Analysis

A comprehensive network traffic analysis and malware infection chain investigation based on practical packet captures (PCAP).

## 🚀 Project Overview
This repository contains a detailed forensic investigation of a malicious network traffic capture. The analysis uncovers a classic multi-stage malware infection vector, starting from an initial phishing email and leading to Cobalt Strike C2 beaconing, external reconnaissance, and malspam distribution.

---

## 🔗 The Infection Chain
The attack followed a textbook multi-stage execution flow:
1. **Phishing Email:** Initial delivery via a deceptive, trust-building email.
2. **Word Doc:** Opening of a malicious Word document attached to the email.
3. **Enable Content:** User interaction enabling macros/content execution.
4. **ZIP Download:** Background downloading of a password-protected or malicious archive (`document.zip`).
5. **XLS Payload:** Extraction and execution of the spreadsheet payload (`chart-1530076591.xls`).
6. **C2 Beaconing:** Establishing persistent command and control communication.
7. **Post-Infection Traffic:** Secondary data exchange with hostile domains.
8. **IP Lookup (`api.ipify.org`):** Automatic external IP and location reconnaissance.
9. **Malspam:** Utilizing the compromised host to broadcast automated spam emails.

---

## 📊 Investigation Summary & Findings

### Part 1: Initial Compromise & C2 Detection
* **First HTTP Connection:** `2021-09-24 16:44:38` (Targeting malicious IP via `attirenepal.com`).
* **Downloaded Payload:** `document.zip` containing `chart-1530076591.xls`.
* **Server Infrastructure:** LiteSpeed / PHP/7.2.34.
* **Malicious Domains Identified:** `finejewels.com.au`, `thietbiagt.com`, `new.americold.com`.
* **Cobalt Strike C2 Servers:** 
  * `185.106.96.158` (`survmeter.live`)
  * `185.125.204.174` (`securitybusinpuff.com`)

### Part 2: Post-Infection & Exfiltration
* **Post-Infection Traffic Domain:** `maldivehost.net` (Initial POST request length: 281 bytes).
* **External IP Reconnaissance:** DNS query to `api.ipify.org` at `2021-09-24 17:00:04`.
* **Malspam Activity:** SMTP session initiated with `MAIL FROM: farshin@mailfa.com` (Total 1439 SMTP traffic frames).

---

## 🛠️ Tools & Wireshark Filters Used
* **Tools:** Wireshark, VirusTotal, MITRE ATT&CK, WHOIS.
* **Key Wireshark Filters:**
  * `http` — To track initial web traffic and file downloads.
  * `tls.handshake.type == 1` — To capture Client Hello packets and inspect SNI (Server Name Indication) for hidden domains.
  * `dns.qry.name contains "api"` — To filter specific external API reconnaissance queries.
  * `smtp && frame contains "MAIL FROM"` — To analyze mail spam propagation vectors.

---

## 📌 Indicators of Compromise (IOCs)

| Indicator Type | Value | Description / Role |
|---|---|---|
| Domain | `attirenepal.com` | Initial hosting domain for `document.zip` |
| File Name | `document.zip` / `chart-1530076591.xls` | Malicious archive and spreadsheet payload |
| Domain | `finejewels.com.au` | Secondary infrastructure domain |
| Domain | `thietbiagt.com` | Secondary infrastructure domain |
| Domain | `new.americold.com` | Secondary infrastructure domain |
| IP / Domain | `185.106.96.158` (`survmeter.live`) | Cobalt Strike C2 Server 1 |
| IP / Domain | `185.125.204.174` (`securitybusinpuff.com`) | Cobalt Strike C2 Server 2 |
| Domain | `maldivehost.net` | Post-infection communication domain |
| Domain | `api.ipify.org` | External IP check service |
| Email | `farshin@mailfa.com` | Sender address used in malspam phase |
