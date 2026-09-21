# 🎯 Threat Hunting Lab: SMB Brute-Force & Evasion Tactics (Red vs. Blue)

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

<img width="1791" height="875" alt="1" src="https://github.com/user-attachments/assets/40674640-fe73-4701-ba80-7e102873d351" />



### 2. Reconnaissance (Nmap)
A comprehensive network scan was initiated to map the target's attack surface. The scan confirmed that port `445/tcp` (SMB) was open, presenting a potential entry point.

```bash
sudo nmap -A -T4 192.168.100.77
