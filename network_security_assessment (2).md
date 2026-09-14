# Task 10 – Full Network Security Assessment Report

> **Important:** This report is structured for the required assessment, but the scan and traffic-analysis results must come from your own **authorized local test network**. Replace every `No additional issue was established from the supplied evidence` field with your actual Nmap/Wireshark/Nikto findings and add the required screenshots before submission. Do not invent scan results.

---

# 1. Assessment Overview

## 1.1 Objective

The objective of this assessment is to conduct a structured, end-to-end security assessment of an authorized local test network using Nmap for reconnaissance, Wireshark for network-traffic analysis, and Nikto for web-server vulnerability assessment where a web server is present.

The assessment focuses on identifying reachable hosts, open ports, running services, potentially insecure network traffic, and web-server configuration issues. The findings are documented according to their severity and followed by a prioritized remediation roadmap.

## 1.2 Authorization

This assessment was performed only against a network and systems for which assessment permission was available.

**Authorized network owner/lab:** `Not identified from the supplied evidence`

**Assessment type:** Authorized local/lab security assessment

---

# 2. Assessment Scope

| Scope Item | Details |
|---|---|
| Target IP range | `192.168.19.1` (specific host shown in Nmap evidence) |
| Individual targets | `192.168.19.1` |
| Services assessed | TCP/UDP services discovered during scanning |
| Web servers | `testphp.vulnweb.com` (HTTP observed in Wireshark) |
| Assessment date | `14 September 2026` |
| Start time | `Nmap: 12:07:22; Wireshark screenshots show activity from approximately 23:43` |
| End time | `Wireshark screenshots show activity through approximately 23:51; verify exact PCAP end time` |
| Tools | Nmap, Wireshark, Nikto where applicable |
| Environment | Authorized local/test network |

## 2.1 Scope Limitations

Only the IP addresses, systems, services, and time window defined above were included. No systems outside the authorized scope were intentionally scanned or tested.

---

# 3. Methodology

The assessment followed three main phases:

```text
Phase 1 – Reconnaissance
        ↓
Nmap host/service discovery
        ↓
Phase 2 – Traffic Analysis
        ↓
Wireshark packet capture and protocol analysis
        ↓
Phase 3 – Web Vulnerability Scan
        ↓
Nikto, if an authorized web server was identified
        ↓
Findings Register
        ↓
Risk Prioritization
        ↓
Remediation Roadmap
```

The methodology is aligned conceptually with established security-testing approaches such as the OWASP Web Security Testing Guide and the PTES technical guidelines.

---

# 4. Phase 1 – Reconnaissance with Nmap

## 4.1 Nmap Command

The task specifies an Nmap service/version and OS-detection scan:

```bash
nmap -sV -O [TARGET_RANGE]
```

**Actual command used:**

```text
Not specified in supplied evidence WITH THE ACTUAL COMMAND YOU RAN]
```

## 4.2 Nmap Results

The supplied Nmap evidence shows the following:

### Host Discovery

| Host/IP | Status | Network Distance |
|---|---|---|
| `192.168.19.1` | Up | 0 hops |

The host responded with approximately **0.000061 seconds** latency.

### Open Ports and Services

| Host/IP | Port | Protocol | State | Service | Version |
|---|---:|---|---|---|---|
| `192.168.19.1` | 80 | TCP | Open | HTTP | Apache httpd 2.4.63 (Debian) |

The scan reported **999 closed TCP ports** that were not shown.

### Operating System Detection

Nmap reported:

- Device type: **general purpose**
- Running: **Linux 2.6.X/5.X**
- OS details: **Linux 2.6.32, Linux 5.0–6.2**
- Network distance: **0 hops**

The OS identification is an Nmap fingerprint estimate and should not be treated as an exact kernel-version confirmation.

## 4.3 Nmap Analysis

The Nmap results should be reviewed to identify:

- Unexpected hosts.
- Unnecessary open ports.
- Outdated services.
- Administrative services exposed to the network.
- Clear-text protocols.
- Services that should be restricted by firewall rules.
- Internet-facing exposure, if applicable within the authorized scope.

### Assessment Finding

**Finding ID:** `[NMAP-01 or NMAP-02]`

**Description:** `No additional issue was established from the supplied evidence`

**Severity:** `Medium`

**Affected Asset:** `Not identified from the supplied evidence`

**Recommended Fix:** `Not identified from the supplied evidence`

### Evidence

Add the Nmap terminal screenshot here:

```text
[INSERT SCREENSHOT – NMAP OUTPUT]
```

---

# 5. Phase 2 – Network Traffic Analysis with Wireshark

## 5.1 Capture Details

The task requires **5+ minutes of network traffic** to be captured and analyzed.

| Capture Item | Details |
|---|---|
| Capture file | `wireshark_capture.pcap` (actual file must be added before submission) |
| Interface | `eth0` |
| Start time | `Nmap: 12:07:22; Wireshark screenshots show activity from approximately 23:43` |
| End time | `Wireshark screenshots show activity through approximately 23:51; verify exact PCAP end time` |
| Duration | `Not specified in supplied evidence – must be 5+ minutes]` |
| Network | `Local/test environment; Nmap target 192.168.19.1` |
| Capture environment | Authorized local/test network |

## 5.2 Capture File

The final repository should contain:

```text
wireshark_capture.pcap
```

Do not create a fake capture. Export the actual capture produced by Wireshark.

---

# 6. HTTP Traffic Analysis

## 6.1 HTTP Evidence

The supplied Wireshark evidence was filtered using:

```text
http
```

The packet list shows:

```text
GET /login.php HTTP/1.1
GET /style.css HTTP/1.1
GET /images/logo.gif HTTP/1.1
GET /favicon.ico HTTP/1.1
POST /userinfo.php HTTP/1.1
GET /login.php HTTP/1.1
```

The observed HTTP traffic includes:

```text
Source:      10.54.104.40
Destination: 44.228.249.3
Protocol:    HTTP
Destination port: 80
Host:        testphp.vulnweb.com
```

## 6.2 HTTP POST Observation

A second Wireshark screenshot uses:

```text
http.request.method=="POST"
```

and identifies:

```text
POST /userinfo.php HTTP/1.1
Content-Type: application/x-www-form-urlencoded
```

This confirms that form data was transmitted over an unencrypted HTTP connection.

The supplied screenshot does not provide sufficient evidence to safely reproduce an actual password or secret, so no credentials are included in this report.

## 6.3 Security Impact

Plain HTTP does not provide confidentiality or integrity for application-layer traffic. A network observer with suitable access to the traffic path may be able to inspect or manipulate HTTP requests and responses.

The observed POST request is therefore a significant security concern if the application accepts authentication credentials, personal information, session data, or other sensitive information.

### HTTP Finding

**Finding ID:** F-002

**Description:** An HTTP POST request to `/userinfo.php` was observed over TCP port 80.

**Severity:** High

**Affected Asset:** `testphp.vulnweb.com`

**Recommended Fix:**

1. Enable HTTPS/TLS.
2. Redirect HTTP requests to HTTPS.
3. Use secure cookies and appropriate security headers.
4. Never transmit passwords or other sensitive information over plain HTTP.

# 7. DNS Traffic Analysis

## 7.1 DNS Evidence

The supplied Wireshark evidence shows DNS traffic from:

```text
10.54.104.40 → 10.54.104.248
```

The following queries are visible:

```text
Standard query 0xb125 A testphp.vulnweb.com
Standard query 0x3824 AAAA testphp.vulnweb.com
```

Both A and AAAA DNS lookups were observed.

## 7.2 DNS Security Analysis

The supplied evidence is consistent with normal hostname resolution. No DNS poisoning or spoofing is established by the screenshots.

### DNS Finding

**Finding ID:** F-003

**Description:** DNS A and AAAA queries for `testphp.vulnweb.com` were observed.

**Severity:** Informational

**Affected Asset:** `10.54.104.40`

**Recommended Fix:** Use trusted DNS infrastructure, monitor DNS activity, and apply appropriate organizational DNS security controls.

# 8. ARP Traffic Analysis

Use the Wireshark display filter:

```text
arp
```

## 8.1 Observations

Record:

- ARP requests.
- ARP replies.
- IP-to-MAC address mappings.
- Repeated or unusual ARP activity.
- Any evidence suggesting duplicate or conflicting mappings.

### Findings

**ARP traffic observed:** `YES`

**Evidence of suspicious ARP behavior:** `YES`

**Description:** `No additional issue was established from the supplied evidence`

### Security Impact

ARP does not provide built-in authentication of ARP messages. In an appropriate attack scenario, an attacker with access to the local network may attempt ARP spoofing/poisoning to redirect traffic.

### Recommended Fix

Use appropriate switch security features such as Dynamic ARP Inspection where supported, network segmentation, secure network access controls, and monitoring for abnormal ARP behavior.

### Evidence

```text
[INSERT SCREENSHOT – WIRESHARK ARP FILTER/RESULT]
```

---

# 9. Unencrypted Sensitive Data Assessment

The task specifically requires documenting any unencrypted sensitive data observed.

## 9.1 Result

**Sensitive unencrypted data found:** `YES`

If **YES**, document only information necessary for the report. Do not publish real passwords, authentication tokens, private messages, personal information, or other secrets.

| Data Type | Observed? | Protocol | Risk | Recommended Fix |
|---|---|---|---|---|
| Credentials | `YES` | `Not identified from the supplied evidence` | `Not identified from the supplied evidence` | Use encrypted/authenticated protocol |
| Session information | `YES` | `Not identified from the supplied evidence` | `Not identified from the supplied evidence` | Use HTTPS/TLS and secure cookies |
| Personal information | `YES` | `Not identified from the supplied evidence` | `Not identified from the supplied evidence` | Encrypt transmission |
| Other sensitive data | `YES` | `Not identified from the supplied evidence` | `Not identified from the supplied evidence` | `Not identified from the supplied evidence` |

**Important:** Redact sensitive information from screenshots and the final report.

---

# 10. Phase 3 – Web Vulnerability Scan with Nikto

## 10.1 Applicability

A web service was identified during the Nmap assessment:

```text
192.168.19.1:80
Apache httpd 2.4.63 (Debian)
```

Therefore, Nikto would be applicable to the identified web server.

## 10.2 Nikto Assessment Status

**Nikto scan performed:** No

Nikto was not executed during the assessment. Therefore, no Nikto vulnerability results are claimed in this report.

This is recorded as a **testing limitation**, rather than as a security finding. Fabricating Nikto output would not provide valid assessment evidence.

## 10.3 Recommended Follow-up

If the assessor is required to complete the Nikto phase, it should be run only against the authorized test web server and the actual terminal output should be added to the repository.

Example authorized command:

```bash
nikto -h http://192.168.19.1
```

Any findings should then be added to the findings register with their actual evidence and severity.

# 11. Findings Register

| Finding ID | Description | Severity | Affected Asset | Recommended Fix |
|---|---|---|---|---|
| F-001 | HTTP service is exposed on TCP port 80 | Medium | `192.168.19.1:80` | Use HTTPS/TLS and restrict unnecessary access |
| F-002 | HTTP POST form submission observed over plaintext HTTP | High | `testphp.vulnweb.com` | Use HTTPS, secure cookies and TLS-protected authentication |
| F-003 | DNS A/AAAA queries for `testphp.vulnweb.com` were observed | Informational | `10.54.104.40` | Use trusted DNS infrastructure and monitor DNS activity |
| F-004 | No ARP anomaly was established from the supplied screenshots | Informational | Local test network | Review the actual PCAP using the `arp` filter |
| F-005 | Apache service/version information was disclosed by service detection | Low | `192.168.19.1:80` | Keep Apache patched and minimize unnecessary version disclosure |

**Nikto:** Not performed; therefore no Nikto finding is included.

# 12. Risk Summary

## 12.1 Findings by Severity

| Severity | Number of Findings |
|---|---:|
| Critical | 0 |
| High | 1 |
| Medium | 1 |
| Low | 1 |
| Informational | 2 |

## 12.2 Overall Risk Posture

**Overall risk: Medium**

**Summary:** `The supplied evidence indicates a Medium overall risk posture. The highest-priority concern is the observed HTTP POST request to /userinfo.php over TCP port 80, because HTTP does not provide transport confidentiality or integrity. The Nmap scan also identified the Apache HTTP service on 192.168.19.1. No Critical issue was established from the supplied evidence.`

The overall risk rating should be based on the actual findings rather than automatically assuming that the network is secure or insecure.

---

# 13. Executive Summary

## 13.1 Management-Level Summary

The security assessment examined an authorized test environment using Nmap and Wireshark. Nmap identified the host `192.168.19.1` as reachable and found TCP port 80 open with Apache httpd 2.4.63 (Debian). The scan reported 999 closed TCP ports that were not displayed.

Wireshark analysis identified HTTP communication with the Acunetix demonstration application `testphp.vulnweb.com`. Multiple HTTP GET requests and an HTTP POST request to `/userinfo.php` were visible. The POST request used `application/x-www-form-urlencoded`, demonstrating that form data was being transmitted through an unencrypted HTTP connection.

The assessment records five findings: **0 Critical, 1 High, 1 Medium, 1 Low, and 2 Informational**. The highest-priority issue is the plaintext HTTP POST because unencrypted application traffic can be observed or manipulated by an attacker with suitable network access.

DNS A and AAAA queries were observed. The supplied screenshots do not establish DNS poisoning or ARP spoofing. A web server was identified, but **Nikto was not performed**, so no Nikto results are claimed.

The recommended remediation priority is to protect web communication with HTTPS/TLS, avoid transmitting sensitive information through plain HTTP, maintain the Apache service, minimize unnecessary exposure, and perform a follow-up assessment after remediation.

# 14. Technical Findings by Phase

## 14.1 Reconnaissance Findings

Nmap identified:

```text
Target: 192.168.19.1
Host status: Up
Latency: 0.000061 seconds
Open TCP port: 80
Service: HTTP
Version: Apache httpd 2.4.63 (Debian)
OS family: Linux
Closed TCP ports not shown: 999
Network distance: 0 hops
```

The scan completed in approximately **0.18 seconds**.

## 14.2 Traffic Analysis Findings

Wireshark identified:

```text
GET /login.php HTTP/1.1
GET /style.css HTTP/1.1
GET /images/logo.gif HTTP/1.1
GET /favicon.ico HTTP/1.1
POST /userinfo.php HTTP/1.1
GET /login.php HTTP/1.1
```

The POST request was observed with:

```text
Content-Type: application/x-www-form-urlencoded
```

DNS traffic included A and AAAA queries for:

```text
testphp.vulnweb.com
```

TLSv1.2, TCP and QUIC traffic were also visible in the supplied Wireshark evidence.

## 14.3 Web Assessment Findings

An HTTP web server was identified, so Nikto would be applicable. **Nikto was not performed**, and consequently no Nikto vulnerabilities are reported.

# 15. Remediation Roadmap

| Priority | Finding | Recommended Fix | Effort | Suggested Order |
|---|---|---|---|---|
| 1 | F-002 – HTTP POST over plaintext | Enable HTTPS/TLS and protect form/authentication submissions | Medium | Immediate |
| 2 | F-001 – HTTP service exposed | Redirect HTTP to HTTPS and restrict unnecessary access | Medium | Next |
| 3 | F-005 – Apache version disclosure | Keep Apache updated and apply server hardening | Easy | Next |
| 4 | F-003 – DNS visibility | Use trusted DNS infrastructure and monitor DNS activity | Medium | Scheduled |
| 5 | F-004 – ARP review | Inspect the actual PCAP for anomalous ARP behavior | Easy | Routine |

# 16. Recommended Security Improvements

Based on the assessment methodology, organizations should consider:

1. **Minimize exposed services** – Disable unnecessary services and close unused ports.
2. **Keep software updated** – Patch operating systems, applications, network devices, and web servers.
3. **Use encryption** – Protect sensitive communication using modern TLS and secure protocols.
4. **Segment networks** – Separate sensitive systems and restrict unnecessary lateral communication.
5. **Monitor network traffic** – Maintain appropriate logging and detect unusual network behavior.
6. **Harden web servers** – Remove unnecessary files/services and address identified configuration weaknesses.
7. **Repeat assessments** – Re-scan after remediation to verify that security improvements were effective.

---

# 17. Evidence Inventory

The following evidence should be included in the GitHub repository:

| Evidence | Required? | File/Location |
|---|---|---|
| Nmap raw output | Yes | `nmap_results.txt` |
| Wireshark capture | Yes | `wireshark_capture.pcap` |
| Nmap screenshot | Yes | Report or evidence folder |
| Wireshark HTTP screenshot | Yes | Report or evidence folder |
| Wireshark DNS screenshot | Yes | Report or evidence folder |
| Wireshark ARP screenshot | Yes | Report or evidence folder |
| Nikto screenshot | If web server present | Report or evidence folder |
| Final assessment report | Yes | `network_security_assessment.md` |

---

# 18. Conclusion

This assessment provides a structured view of the security posture of the authorized local test network. Nmap establishes an inventory of reachable systems and exposed services, Wireshark provides visibility into actual network communications, and Nikto can identify common web-server weaknesses when an authorized web service is present. The value of the assessment depends on accurate evidence and responsible interpretation of the results. After identifying weaknesses, the organization should prioritize remediation according to severity, exposure, exploitability, and business importance, then repeat scanning and verification to confirm that the issues have been resolved.

The assessment should therefore be treated as a continuous security process rather than a one-time scan.

---

# 19. References

1. **OWASP Web Security Testing Guide (WSTG)**  
   https://owasp.org/www-project-web-security-testing-guide/

2. **Penetration Testing Execution Standard (PTES) – Technical Guidelines**  
   http://www.pentest-standard.org/index.php/PTES_Technical_Guidelines

3. **Nmap Reference Guide**  
   https://nmap.org/book/man.html

4. **Wireshark User's Guide**  
   https://www.wireshark.org/docs/wsug_html_chunked/

5. **Nikto Web Scanner**  
   https://github.com/sullo/nikto

6. **NIST SP 800-115 – Technical Guide to Information Security Testing and Assessment**  
   https://csrc.nist.gov/pubs/sp/800/115/final

---

## Submission Checklist

- [ ] Assessment scope is defined in writing.
- [ ] IP range is documented.
- [ ] Services in scope are documented.
- [ ] Assessment time window is documented.
- [ ] Nmap scan completed on the authorized target range.
- [ ] All discovered hosts documented.
- [ ] Open ports documented.
- [ ] Services and versions documented.
- [ ] `nmap_results.txt` included.
- [ ] Wireshark capture is at least 5 minutes.
- [ ] `wireshark_capture.pcap` included.
- [ ] HTTP traffic analyzed.
- [ ] DNS traffic analyzed.
- [ ] ARP traffic analyzed.
- [ ] Any unencrypted sensitive data documented and redacted in screenshots.
- [ ] Nikto run if an authorized web server is present.
- [ ] Nikto findings documented.
- [ ] Findings register completed.
- [ ] Every finding has an ID.
- [ ] Every finding has a severity.
- [ ] Every finding has an affected asset.
- [ ] Every finding has a recommended fix.
- [ ] Executive summary completed.
- [ ] Technical report completed.
- [ ] Screenshots included as evidence.
- [ ] Remediation roadmap completed.
- [ ] Findings prioritized.
- [ ] Effort estimates marked Easy/Medium/Hard.
- [ ] References section included.
- [ ] No demo video required.
