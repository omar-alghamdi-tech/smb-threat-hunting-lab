# SMB Brute Force Attack & Detection (Red vs. Blue Team Lab)

## 📌 Objective
The objective of this home lab exercise is to simulate a realistic brute-force attack against the SMB protocol (Red Team) and successfully hunt for and detect the malicious activity using Windows Event Logs (Blue Team). This use case demonstrates the ability to pivot when traditional tools fail and highlights core Threat Hunting methodologies.

## 🛠️ Environment & Tools
* **Attacker Machine:** Kali Linux (`nmap`, `hydra`, `NetExec/nxc`)
* **Target Machine:** Windows 10 (`192.168.100.77`)
* **Detection:** Windows Event Viewer (Security Logs)

---

## 🔴 Phase 1: Red Team (Reconnaissance & Attack)

### 1. Lab Setup
The lab consists of an isolated VirtualBox network where the Kali Linux attacker machine can reach the Windows 10 victim machine.
![Lab Setup](images/1.jpg)

### 2. Reconnaissance (Nmap)
Before launching an attack, a thorough network scan was conducted to identify open ports and services. The scan revealed that port `445` (SMB) is open.
```bash
sudo nmap -A -T4 192.168.100.77
