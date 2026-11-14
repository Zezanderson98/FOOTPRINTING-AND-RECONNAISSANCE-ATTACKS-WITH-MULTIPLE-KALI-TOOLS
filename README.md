
# NETWORKWALKS-B084-WK2-PM1-FOOTPRINTING-AND-RECONNAISSANCE



<div align="center">

#  🔍 Ethical Hacking Lab: Footprinting & Reconnaissance

**Gathering Domain, DNS, Web Stack & WAF Intelligence with Kali Linux Tools**
</div>

<p align="center">
<img width="118" height="20" alt="image" src="https://github.com/user-attachments/assets/70b04217-936e-4b48-b2e3-835b3ff824a4" />
<img width="105" height="20" alt="image" src="https://github.com/user-attachments/assets/413a4b7b-a720-4366-843b-986d57c52d55" />
<img width="135" height="20" alt="image" src="https://github.com/user-attachments/assets/24cf63ba-9ddf-42ed-9627-899b2ad98dd0" />
<img width="141" height="20" alt="image" src="https://github.com/user-attachments/assets/88827703-d22f-4ad8-9ddf-ffd105cc9812" />
<img width="71" height="20" alt="image" src="https://github.com/user-attachments/assets/5d7319fb-71e6-4def-b197-15c8b3f1ab45" />
<img width="119" height="20" alt="image" src="https://github.com/user-attachments/assets/363c0f27-348c-4c34-9795-3f0888dff770" />
<img width="74" height="20" alt="image" src="https://github.com/user-attachments/assets/b3bf8fb9-a287-437a-9139-3752f26988d0" />
<img width="92" height="20" alt="image" src="https://github.com/user-attachments/assets/e8e2ddd8-7e1f-4dd9-9d4e-dc0114fd30c3" />
<img width="94" height="20" alt="image" src="https://github.com/user-attachments/assets/395ea438-febb-47e3-b13a-9f2ddfca053e" />
<img width="76" height="20" alt="image" src="https://github.com/user-attachments/assets/4e7077f0-ca66-42cf-9050-72708298c5bf" />
<img width="92" height="20" alt="image" src="https://github.com/user-attachments/assets/9b2de962-e708-4a87-862d-76fa62740d01" />
<img width="90" height="20" alt="image" src="https://github.com/user-attachments/assets/9773d968-50cf-4123-96e7-0fd4cc9c95fd" />
   <img width="171" height="20" alt="image" src="https://github.com/user-attachments/assets/0e13183f-78f3-45d0-bec9-0daae375ee8e" />





---

## 📋 Project Overview
**Reconnaissance (Footprinting)** is the critical first phase of any ethical hacking engagement or penetration test. Before interacting directly with a target system, a security analyst quietly collects publicly available intelligence to map out domain ownership, hosting infrastructure, DNS/mail routing, web technologies, and perimeter defenses—without triggering alerts on the target system.

In this lab (**Week 2 | Project Module 1**), six built-in **Kali Linux** reconnaissance utilities were executed against `networkwalks.com` to build a comprehensive intelligence profile of the target:
1. `whois` — Domain registration & registrar intelligence
2. `whatweb` — Web technology & CMS fingerprinting
3. `nslookup` — DNS-to-IP address resolution
4. `curl` — HTTP response header inspection
5. `wafw00f` — Web Application Firewall (WAF) detection
6. `dnsrecon` — Comprehensive DNS record enumeration

---

## 🎯 Objectives
* **Gather Domain & Ownership Intelligence:** Query public WHOIS records to identify domain registration details, expiration timelines, and authoritative name servers (`whois`).
* **Enumerate DNS Infrastructure:** Resolve the target domain to its origin IPv4 address and map all DNS records (`A`, `NS`, `MX`, `SPF`, `TXT`, and `SRV`) using `nslookup` and `dnsrecon`.
* **Fingerprint Web Technologies & Headers:** Identify the underlying web server, CMS version, active plugins, frameworks, and HTTP response headers using `whatweb` and `curl -I`.
* **Detect Perimeter Defenses:** Probe the target web application to identify Web Application Firewall (WAF) protections using `wafw00f`.

---

## 💡 Purpose of the Lab
The purpose of this laboratory is to practice the **reconnaissance (footprinting) phase** of an ethical hacking engagement—the critical first step before any active vulnerability scanning or exploitation takes place. Using only publicly accessible records and standard protocol queries, this lab demonstrates how an analyst builds a complete technical profile of a target (`networkwalks.com`) without directly attacking it, and explains how organizations can reduce public metadata leakage to strengthen their security posture.

---

## 🏗️ Lab Architecture & Network Topology
The reconnaissance workflow operates from an isolated **Kali Linux** workstation querying public internet infrastructure and the authorized target web server:

```text
+------------------------------------------+

|        Kali Linux VM (VirtualBox)        |
|------------------------------------------|
| • Attacker / Analyst Workstation         |
| • Internal VM IP: 10.0.2.15 (NAT Mode)   |
| • Tools: whois, whatweb, nslookup,       |
|          curl, wafw00f, dnsrecon         |
+--------------------+---------------------+
                     |
                     | Outbound Passive & Active OSINT Queries
                     | (WHOIS / DNS / HTTP & HTTPS)
                     v
+------------------------------------------+

|      VirtualBox NAT Gateway & Host       |
|------------------------------------------|
| • Virtual Gateway: 10.0.2.1              |
| • Routes encrypted/public queries to WAN |
+--------------------+---------------------+
                     |
                     | Public Internet
         +-----------+-----------+

         |                       |
         v                       v
+---------------------+ +------------------------------------------+

| Public Registries & | |       Target Web Infrastructure          |
|    DNS Servers      | |------------------------------------------|
|---------------------| | • Domain: networkwalks.com               |
| • GoDaddy WHOIS     | | • Origin IP: 192.232.216.135             |
| • HostGator NS      | | • Hosting: HostGator (Bind 9.16.23-RH)   |
|   (ns6135 / ns6136) | | • WAF: ModSecurity (SpiderLabs)          |
| • Google DNS (8.8.8.8)| • Stack: Apache + WordPress 7.0.4        |
+---------------------+ +------------------------------------------+
```

### Topology Flow Breakdown
1. **Analyst Workstation (Kali Linux):** Initiates all six reconnaissance commands from an isolated VirtualBox VM configured in **NAT mode (`10.0.0.2`)**.
2. **Public Registries & DNS Layer:** `whois`, `nslookup`, and `dnsrecon` query public registrar databases (GoDaddy) and authoritative name servers (`://hostgator.com`, `://hostgator.com`) to map domain ownership, mail servers, and cPanel `SRV` records.
3. **Target Web Layer (`192.232.216.135`):** `whatweb`, `curl -I`, and `wafw00f` send standard HTTP/HTTPS requests through the **ModSecurity WAF** to inspect server headers, API endpoints (`/wp-json/`), and CMS versions (`WordPress 7.0.4`).




















## 🛠️ Lab Tasks, Commands & Intelligence Findings

### Task 1: Domain Registration & WHOIS Intelligence
* **Objective:** Query public WHOIS records to identify domain ownership, registration/expiration dates, registrar details, and authoritative name servers.
* **Command Executed:**
  ```bash
  whois networkwalks.com
  ```
* **Observed Findings:**
  * **Registrar:** GoDaddy.com, LLC
  * **Creation / Expiry Dates:** Registered `2019-11-06` | Expires `2027-11-06`
  * **Authoritative Name Servers:** `://hostgator.com`, `://hostgator.com`
* **Attacker Perspective:** Name servers reveal **HostGator** as the underlying hosting provider, while registration timelines and abuse contacts assist in infrastructure profiling and social engineering planning.

---

### Task 2: Web Technology Fingerprinting
* **Objective:** Identify the web server, Content Management System (CMS), active plugins, frontend frameworks, and server IP.
* **Command Executed:**
  ```bash
  whatweb networkwalks.com
  ```
* **Observed Findings:**
  * **Web Server & Hosting Location:** Apache | United States (`192.232.216.135`)
  * **CMS & Plugins:** **WordPress 7.0.4**, **WP Download Manager 3.3.58**
  * **Frameworks & Libraries:** Bootstrap 7.0.4, jQuery 3.7.1, HTML5
  * **Exposed Contact:** `info@networkwalks.com`
* **Attacker Perspective:** Exposing exact software and plugin versions enables attackers to query CVE and exploit databases for known, unpatched vulnerabilities.

---

### Task 3: DNS Resolution (`nslookup`)
* **Objective:** Resolve the target domain name into its underlying IPv4 address via DNS.
* **Command Executed:**
  ```bash
  nslookup networkwalks.com
  ```
* **Observed Findings:**
  * **Resolved IPv4 Address:** `192.232.216.135`
* **Attacker Perspective:** Resolving the origin IP allows an attacker to perform direct port scanning, run reverse-IP lookups to find co-hosted domains, and map network boundaries.

---

### Task 4: HTTP Response Header Inspection
* **Objective:** Inspect raw HTTP response headers to analyze server banners, caching layers, cookies, and hidden endpoints.
* **Command Executed:**
  ```bash
  curl -I https://networkwalks.com
  ```
* **Observed Findings:**
  * **HTTP Status:** `HTTP/2 200`
  * **Server & Caching Stack:** `server: Apache`, `x-nginx-cache: WordPress`, `x-endurance-cache-level: 0`
  * **Exposed API Endpoint:** `</wp-json/>; rel="https://w.org"` (WordPress REST API)
  * **Cookies & Security Headers:** `__wpdm_client` (`Secure; HttpOnly`), `permissions-policy`, and `referrer-policy`
* **Attacker Perspective:** HTTP headers reveal backend server architecture, caching configurations, and API endpoints without requiring full page rendering.

---

### Task 5: Web Application Firewall (WAF) Detection
* **Objective:** Determine whether a Web Application Firewall is actively filtering traffic to the target site.
* **Command Executed:**
  ```bash
  wafw00f networkwalks.com
  ```
* **Observed Findings:**
  * **WAF Identified:** **ModSecurity (SpiderLabs)**
* **Attacker Perspective:** Confirming a WAF is present tells the tester that noisy or generic attack payloads will be blocked or logged, shaping subsequent evasion or testing strategies.

---

### Task 6: Comprehensive DNS Enumeration
* **Objective:** Enumerate all DNS record types including `SOA`, `NS`, `MX`, `SPF`/`TXT`, and Service (`SRV`) records.
* **Command Executed:**
  ```bash
  dnsrecon -d networkwalks.com
  ```
* **Observed Findings:**
  * **SOA & Name Servers:** `://hostgator.com` (`50.87.144.87`), `://hostgator.com` (`192.232.216.131`)
  * **DNS Server Banner:** `Bind 9.16.23-RH`
  * **Mail Exchange (`MX`):** `://networkwalks.com` (`192.232.216.135`)
  * **TXT / SPF Records:** `v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all` and Google Site Verification token
  * **Service (`SRV`) Records:** Multiple `_autodiscover._://networkwalks.com` records pointing to `cpanlemaildiscovery.cpanel.net`
* **Attacker Perspective:** Maps the target's entire DNS, email routing, and cPanel hosting infrastructure while revealing the exact DNS server software version.

---

## 🛡️ Defensive Takeaways & Hardening Recommendations
* **Why Footprinting Matters:** Passive reconnaissance relies solely on publicly accessible data, making it virtually undetectable. The less technical metadata an organization leaks, the harder every subsequent stage of an attack becomes.
* **Key Mitigations:**
  * Suppress server banners (`Apache`, `Bind` versions) and CMS/plugin generator tags.
  * Keep WordPress core and plugins (such as WP Download Manager) patched against known vulnerabilities.
  * Restrict unauthenticated access to `/wp-json/` endpoints if not required by public users.
  * Regularly audit public DNS records and keep **ModSecurity WAF** rules updated and monitored.

---

## 📂 Repository Structure
```text
├── README.md
└── screenshots/
    ├── 1_whois.png
    ├── 2_whatweb.png
    ├── 3_nslookup.png
    ├── 4_curl.png
    ├── 5_wafw00f.png
    └── 6_dnsrecon.png
```

---

## 🔐 Security & Ethical Use
This laboratory and repository are intended strictly for educational and authorized research purposes only. All reconnaissance activities were conducted within the authorized [NetworkWalks](https://networkwalks.com) lab scope. Unauthorized scanning or exploitation of external systems is illegal.

## 🔗 Tools & Resources
* **Primary Environment:** Kali Linux Workstation ([NetworkWalks](https://networkwalks.com) Week 2 | Project Module 1)
* **Reconnaissance Suite:** `whois`, `whatweb`, `nslookup`, `curl`, `wafw00f`, `dnsrecon`
* **Official Documentation:** [Kali Linux Tools Reference](https://kali.org)

## 👤 Author
**Anderson Edaiyo Uzezi**  
Cybersecurity Professional B084  
LinkedIn: https://linkedin.com

   











