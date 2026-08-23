# SOC 141 - Phishing URL detected (Emotet C2 Communication)

## Summary
An internal host was found to be communicating with known malicious infrastructure associated with variant 4 of Emotet, including an active C2 server. The network permitted traffic, allowing bidirectional communication. 
A second internal host was identified to having previous communication with the same malicious infrastructure.

**Verdict: True Positive = Active Emotet C2 Communication with Host Compromise**

---

## Alert Details
| Field | Value |
|---|---|
| Date | February 14th, 2021 |
| Time | 12:13PM |
| src IP | 172.16.17.49 |
| dest IP | 91.189.114.8 |
| Port | 80 [HTTP] |
| Action | Allowed |
| User-Agent | `Mozilla/5.0 (Windows NT 6.1; Win64; x64) Chrome/79.0.3945.88` |

---

## Investigation

### src Host Analysis (172.16.17.49)
Src host was observed communicating with two malicious IPs on February 14th at 12:13PM

| Destination | URL | Verdict |
|---|---|---|
| 67.68.210.95:80 | http://67.68.210[.]95/2SjAcA5VhhJiFjBQ/vvszin6AicmidnG5bg/DaDVVYvfEHlcIIcgcu/0U5UiIkaHankrHGa/FYSJmdQDj2ejni1UI/ | Malicious |
| 162.241.242.173:8080 | http://162.241.242[.]173:8080/HQ9TemntfBzghL/3wz57awaSHlQrrnP/S78n2aUqY7U/ | Malicious |

### Dest IP Analysis (91.189.114.8)
- using threat intelligence platforms, this address was confirmed to be a C2 server for Emotet variant 4
- logs describe bidirectional communication with 172.16.17.49 over port 80
- URL associated with communication: http://mogagrocol[.]ru/wp-content/plugins/akismet/fv/index.php?email=ellie@letsdefend.io
- `.ru` domain and the associated wordpress plugin path are consistent with Emotet's use of known-website compromise to utilize as C2 infrastructure

### Lateral Movement/Blast Radius
A second internal host was found to be communicating with the same malicious infrastructure:

| Host | URL | Date |
|---|---|---|
| 172.16.17.14 | http://67.68.210[.]95/HX8tpawYAxLaiFMTGa1/1dG3m5wmqVifrhsZXsG/ | August 29th, 2020 |

This host communicated with the same IP address over 18 months ago, suggesting this infrastructure has been active for an extended period. This additional IP address should be investigated for any prior compromise.

---

## Findings
172.16.17.49 established what was confirmed to be bidirectional communication with known Emotet4 C2 infrastructure. Its user-agent string reflects an outdated chrome version (79.0) on Windows 7 (NT 6.1). This is consistent with common Emotet targets, which are usually unpatched/legacy systems.

The use of a compromised wordpress site (`mogagrocol[.]ru`) as a C2 is consistent with known Emotet TTPs. 

Traffic was permitted through the network, C2 communication was successful. This host must be considered fully compromised.

---

## Conclusion
This is a **True Positive**. Confirmed Emotet4 C2 communication with bidirectional traffic. Infected host has been quarantined.

**Recommended Actions:**
- Maintain quarantine of 172.16.17.49
- Investigate 172.16.17.14 for prior compromise
- Block all identified malicious IPs and domains at the perimeter
- Add Emotet4 payload hash [`SHA-256:04d05978fdb111358073ab0524e5c1fafc0826615c206987618416b8bd8a4747`] to EDR blocklist
- Scan environment for additional hosts that may be communicating with known Emotet infrastructure
- Review email logs for the phishing email initiating infection
- Reset credentials for all accounts used on 172.16.17.49
- Full device reimage must be done before restoration

---
## MITRE ATT&CK MAPPINGS
- **T1566** = Phishing (initial access vector)
- **T1071.001** = Application Layer Protocol: Web Protocols (C2 over HTTP)
- **T1090** = Proxy (compromised wordpress site as C2)
- **T1082** = System Information Discovery
- **T1057** = Process Discovery

