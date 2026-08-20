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

[WIP -- INCOMPLETE]
