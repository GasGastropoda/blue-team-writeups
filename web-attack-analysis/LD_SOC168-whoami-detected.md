# SOC 168 - Whoami Command Detected in Request Body

## Summary
A web shell was detected on an internal web server. It actively executed system comands via HTTP POST commands. All commands returned HTTP 200 with non-zero response sizes. This confirms successful execution. The attacker was able to perform reconaissance and accessed sensitive credential files.

**Verdict: True Positive - Active Web Shell with a Confirmed Compromise**

---

## Alert Details
| Field | Value |
|---|---|
| Date | February 28th, 2022 |
| Time | 04:11AM - 04:15AM |
| Hostname | Webserver1004 |
| Dest IP | 172.16.17.16 |
| Src IP | 61.177.12.87 |
| HTTP Method | POST |
| URL | https://172.16.17[.]16/video/ |
| Action | Allowed |

---

## Investigation

### Src IP Analysis
- the external IP address requires further threat intel lookup
- communications with this address occurred at 4AM, which are outside of normal hours of operation and is therefore anomalous
- logs show no prior communications with this host before malicious communications began

### Target Host Analysis
- Webserver1004 (172.16.17.16)
- Last legitimate login: February 8th, 2022 - 8 days prior to compromise
- suspicious commands were found in device's terminal history

### Attack Timeline
| Time | Command | Response Code | Response Size |
|---|---|---|---|
| 04:11AM | `?c=ls` | 200 | 1021B |
| 04:12AM | `?c=whoami` | 200 | 912B |
| 04:13AM | `?c=uname` | 200 | 910B |
| 04:!4AM | `?c=cat /etc/passwd` | 200 | 1321B |
| 04:15AM | `?c=cat /etc/shadow/` | 200 | 1501B |

---

## Findings

All POST requests returned a HTTP 200 with notable response sizes. This confirmed that the attacks were successfully executed on the server, their outpuyt being returned to the attacker. The `?c=` parameter is constituent with a web shell accepting OS commands.

**Attack Progression:**
1. `ls` = enumeration of directories; also a form of confirming cmd execution
2. `whoami` = identifying user context
3. `unam3` = OS and kernel reconnaissance
4. `cat /etc/passwd/` = enumeration of all system users
5. `cat /etc/shadow/` = attempting to retrieve hashed passwords

**Critical Finding:**
the `/etc/shadow` directory contains hashed passwords. If the attacker was able to successfully retrieve these passwords, they could be attempting offline password cracking. All credentials previously used on this system should be immediately considered compromised.

---

## Conclusion

This is a **True Positive**. An active web shell compromise with confirmed command execution and access to sensitive files. This is a critcal incident requiring immediate response.

**Recommended Actions:**
- Isolate the web server immediately
- preserve forensic image before executing remediation
- reset all credentials previously associated with the host
- Identify and remove the web shell from the `/video/` directory
- review how the web shell was deployed
- block the src IP (61.177.12.87) at the perimeter FW
- check all other servers for similar web shells
- Assume breach of `/etc/shadow/` contents

---

## MITRE ATT&CK MAPPINGS
= **T1505.003** = Server Software Component: Web Shell
- **T1033** = System Owner/User Discovery (`whoami`)
- **T1082** = System Information Discovery (`uname`)
- **T1083** = System Information Discovery (`ls`)
- **T1003.008** = OS Credential Dumping: `/etc/passwd` and `/etc/shadow`

