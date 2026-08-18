# LetsDefend: SOC 166 - Javascript Code Detected in Requested URL

## Summary
A reflected XSS attack was detected against an internal web server. The attacker had attempted multiple XSS payloads over an 11 minute period. All malicious injection attmepts were unsuccessful.

**Verdit: True Positive - Attempted Attack with No Compromise**

---

## Alert Details
| Field | Value |
|---|---|
| Date | February 26th, 2022 |
| Time | 06:56PM |
| Hostname | Webserver1002 |
| Target IP Add | 172.16.17.17 |
| Src IP Add | 112.85.42.13 |
| Port | 443 (HTTPS) |
| Action | Allowed |

---

## Investigation

### Src IP Analysis
- using threat intelligence platforms, 112.85.42.13 was discovered to be attributed to China Unicom
- address harbors a poor reputation score; blacklisted on two threat intelligence platforms
- logs show no prior communication with this host before the malicious communications began

### Target Host Analysis
- Webserver1002 (172.16.17.17)
- last login was February 2nd (no recent activity relative to the attack)
- No suspicious processes or logs identified on endpoint

### Attack Timeline
| Time | Payload | Response | Size |
|---|---|---|---|
| 06:45PM | `?q=test` (reconn) | 200 | 885B |
| 06:46PM | `?q=prompt(8)` | 302 | 0B |
| 06:46PM | `<img src=q onerror=prompt(8)>` | 302 | 0B |
| 06:50PM | `<script>for((i)in(self))eval(i)(1)</script>` | 302 | 0B |
| 06:53PM | `<svg><script ?>alert(1)` | 302 | 0B |
| 06:56PM | `<script>javascript:alert(1)</script>` | 302 | 0B |

---

## Findings
Attacker began with a reconnaissance request of `?q=test` that returned a code of HTTP 200 with a response size of 885 bytes, which confirms that the parameter was reflected in the page.
Once they received that confirmation, they escalated to multiple XSS payloads using bypass techniques including:

- Basic script injection
- Image onerror event handlers
- SVG-based script execution
- JS protocol handlers
- Obfuscated eval-based execution

All attempts returned with an HTTP 302 (redirect) with a response size of 0B. This indicates the server did not reflect the payloads.
In other words: the attack wasn't successful.

---

## Conclusion
This is a True Positive. A real XSS attack attempt from a known malicious IP. The attack did not succeed, as the 302 responses confirm the payloads were not executed. No evidence of a compromise.

Recommended actions:
- Block the src IP of 112.85.42.13 at the perimeter firewall
- Reivew the WAF rules for XSS coverage
- Monitor the Webserver 1002 for follow-up activity

---

## MITRE ATT&CK MAPPINGS
- **T1189** - Drive-by compromise
- **T1059.007** - JavaScript execution


