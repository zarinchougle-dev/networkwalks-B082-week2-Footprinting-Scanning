# Networkwalks Cybersecurity Program - Week 2
## Footprinting, Reconnaissance & Network Scanning

This directory contains the practical documentation, terminal outputs, and gathered artifacts for **Week 2** of the B082-Networkwalks Cybersecurity Program. The focus of this week's labs was moving from passive external footprinting to active internal network discovery.

### Completed Modules
* **W2-PM1:** External Reconnaissance via Multiple Kali Tools (Target: `networkwalks.com`)
* **W2-PM2:** Search Engine Intelligence & Directory Harvesting via GHDB (Google Dorks)
* **W2-PM4:** Passive OSINT Infrastructure Gathering via `theHarvester` (Target: `microsoft.com`)
* **W2-PM5:** Local Network Discovery & Topology Mapping via Zenmap / Nmap

---

### Summary of Reconnaissance Findings

#### 1. External Domain Footprinting (`networkwalks.com`)
* **Domain Infrastructure:** Registered via GoDaddy, expires Nov 06, 2027. Authoritative name servers point to HostGator (`ns6135.hostgator.com`, `ns6136.hostgator.com`) with DNSSEC status set to **unsigned**.
* **Network Mapping & Headers:** Resolved successfully to IPv4 `192.232.216.135` and IPv6 `64:ff9b::c0e8:d887`. HTTP response headers reveal an Apache backend server using Nginx caching tailored for WordPress environments, including an exposed REST API endpoint at `/wp-json/`.
* **Security Controls:** A Web Application Firewall (WAF) check via `wafw00f` successfully identified active traffic inspection managed by a **ModSecurity (SpiderLabs) WAF**.
* **DNS Records:** Enumerated 16 distinct infrastructure records (SOA, NS, MX, A, AAAA, TXT, SRV). Identified an active SPF mail policy (`v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all`) and automated cPanel mail discovery SRV paths mapping to port 443.
* **Lab Observations:** An attempt to fingerprint via `WhatWeb` failed with a `No route to host` error across ports 80 and 443, illustrating typical target-side connectivity constraints or firewall drops during active connection queries.

#### 2. Google Hacking Database (GHDB) Practicalities
* **Camera Interface Identification:** Leveraged targeted search strings (e.g., `intitle:"webcamXP" inurl:8080`) to uncover 7 publicly indexed, active camera surveillance interfaces exposing perimeter streaming data.
* **Resource Harvesting:** Utilized the query `intitle:index.of "parent directory" mathematics pdf` to catalog 10 unindexed open directories leaking educational materials directly to search crawlers due to missing server-side indexing configurations.

#### 3. Passive OSINT Extraction (`microsoft.com`)
* **Single-Source Scanning:** Execution via `theHarvester -d microsoft.com -l 1000 -b baidu` returned zero public assets, proving that data harvesting constraints fluctuate drastically based on the search engine used.
* **Multi-Source Mapping Constraints:** Broader scans via `-b all` encountered missing local API key conditions across modular engines (Shodan, Censys, Hunter, VirusTotal). Hudson Rock engines processed successfully but returned 0 active assets under this constrained, unauthenticated layout.

#### 4. Active Internal Network Discovery
* **Local LAN Boundary:** Local test architecture verified at `192.168.56.1` under a `/24` subnet masking layout.
* **Discovery Scanning:** Active ping sweeps targeting `172.16.239.164/24` evaluated 256 addresses within 8.23s, mapping 2 active up-hosts:
  * `172.16.239.164` (Local Host)
  * `172.16.239.219` (Exposing hardware MAC structure: `36:3E:F4:8C:D0:24`)
* **Topology Vectoring:** Visual tracking models generated inside Zenmap accurately linked active targets alongside edge entities such as `172.16.244.138` and `192.168.56.1`.

---

### Risk Log & Defensive Matrix

| Finding / Observation | Technical Exposure | Potential Threat Surface | Remediation Priority |
| :--- | :--- | :--- | :--- |
| **DNSSEC Unsigned** | Lack of cryptographic validation on zone files. | Risk of DNS cache poisoning or path redirection attacks. | Low |
| **HTTP Response Disclosure** | Explicit `Apache`, `WordPress`, and `/wp-json/` header leaks. | Eases malicious technology version mapping and exploit target selection. | Low |
| **Exposed Video Infrastructure** | Publicly reachable, unauthenticated webcam streams. | Critical privacy violations, site monitoring, and hardware exploitation. | Medium |
| **Open Directory Indexing** | Server allows standard browsing of deep filesystems. | Unintentional data leaks, credential theft, and asset exposures. | Low |
| **Undocumented LAN Elements** | Discovery of unmapped active internal IP addresses. | Enlarges internal lateral attack vectors by unknown target machines. | Medium |

---

### Core Cybersecurity Recommendations
1. **Header Sanitization:** Configure the Apache/WordPress production server parameters (`ServerTokens Prod`, `ServerSignature Off`) to eliminate unnecessary technical environment profiling.
2. **Zone File Security:** Enable DNSSEC validation on the domain registrar portal to defend against malicious resolver redirection.
3. **Index Blocking:** Disable global index discovery arrays inside the server settings (e.g., `Options -Indexes` via `.htaccess`) to mitigate open file exploration.
4. **Endpoint Access Controls:** Mandate strong multi-factor authentication (MFA) and restrict access to network management or surveillance boundaries behind a trusted corporate VPN network.
5. **Periodic Audits:** Maintain a routine verification cycle matching automated internal asset sweeping policies with live discovery profiles.

---
**Author:** Zarin Chougle
**Batch:** B082-Networkwalks
**Course:** Practical Cybersecurity Program
