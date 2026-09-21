# 🎯 Threat Hunting Lab: SMB Brute-Force & Evasion Tactics (Red vs. Blue)

---

## 📌 Executive Summary
This home lab simulates a realistic network-based brute-force attack against a Windows 10 endpoint via the SMB protocol. The exercise highlights the evolution of attack vectors—transitioning from legacy tools (Hydra) to modern exploitation frameworks (NetExec)—and concludes with a Threat Hunting phase to detect the attack lifecycle using Windows Security Event Logs.

## 🛠️ Environment & Tools
* **Attacker Machine:** Kali Linux
* **Target Machine:** Windows 10 (`192.168.100.77`)
* **Red Team Toolkit:** `nmap`, `hydra`, `NetExec (nxc)`
* **Blue Team Toolkit:** Windows Event Viewer (Advanced Audit Policies)

---

## 🔴 Phase 1: Red Team (Reconnaissance & Exploitation)

### 1. Network Setup & Target Identification
The lab operates in an isolated environment where the Kali Linux attacker machine targets a standalone Windows 10 host.

![Lab Setup](images/1_2.jpg)

### 2. Reconnaissance (Nmap)
A comprehensive network scan was initiated to map the target's attack surface. The scan confirmed that port `445/tcp` (SMB) was open, presenting a potential entry point.

    sudo nmap -A -T4 192.168.100.77

![Nmap Scan](images/2_2.jpg)

### 3. Attack Obstacle: The Legacy Tool Failure (Hydra)
An initial brute-force attempt was executed using `Hydra`. However, the attack failed entirely, resulting in an `invalid reply` error. 

**💡 Analytical Insight (Why did Hydra fail?):**
Legacy tools like Hydra often struggle against modern Windows 10 endpoints. Windows 10 enforces strict SMB session management, disables SMBv1 by default, and requires modern NTLMv2 authentication. Hydra's parallel connection handling is incompatible with these updated security controls, making it ineffective for modern SMB brute-forcing.

![Hydra Failure](images/3_2.jpg)

### 4. Tactical Pivoting: Modern Exploitation (NetExec)
To bypass the Windows 10 restrictions, the attack was pivoted to **NetExec (nxc)**. 

**💡 Analytical Insight (Why NetExec?):**
NetExec is a modern, stealthy framework built specifically for Active Directory and SMB environments. It natively supports SMBv2/v3, handles modern authentication seamlessly, and provides structured, operational output without crashing the target service.

    nxc smb 192.168.100.77 -u SOC_Victim -p passwords.txt

![NXC Initialization](images/4_2.jpg)

### 5. Successful Compromise
Using the right tool for the job yielded immediate results. NetExec successfully brute-forced the SMB service and retrieved the valid credentials (`Password123`) for the target user `SOC_Victim`.

![NXC Success](images/5_2.jpg)

---

## 🔵 Phase 2: Blue Team (Threat Hunting & Detection)

### 1. Alert Triage & Log Analysis
With the attack successfully executed, the perspective shifted to the Blue Team to hunt for the resulting Indicators of Compromise (IoCs). Advanced Audit Logon Policies were verified on the target machine.

### 2. Identifying the Attack (Event ID 4625 & 4624)
Filtering the Windows Security Logs revealed the complete attack sequence. A burst of **Event ID 4625 (Audit Failure)** confirmed the brute-force attempts, immediately followed by an **Event ID 4624 (Audit Success)**, marking the exact moment the attacker breached the system.

**🚨 Key Forensic Artifacts Captured:**
* **TargetUserName:** `SOC_Victim` (The compromised account)
* **Logon Type:** `3` (Network Logon - proving the attack came over the network via SMB)
* **Source Network Address:** `192.168.100.X` (The Kali Linux Attacker IP)

![Event Viewer Detection](images/6_2.png)

---

## 🛡️ Conclusion & Defensive Recommendations
This simulation proves that while legacy tools fail against modern OS protections, attackers will rapidly pivot to sophisticated frameworks like NetExec. 

**SOC Mitigation & Detection Strategy:**
1. **SIEM Rule Creation:** Configure SIEM alerts to trigger upon detecting a high velocity of `Event ID 4625` originating from a single `Source Network Address` within a 1-minute window, followed by a `4624` for the same user.
2. **Account Lockout Policy:** Enforce an account lockout threshold (e.g., 5 failed attempts) to kill brute-force attacks in their tracks.
3. **Network Segmentation:** Block SMB (port 445) from external networks and restrict it internally only to authorized administrative subnets.
