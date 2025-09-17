# TryHackMe Room: Summit

In this room I practiced analyzing malware samples, identifying malicious activity, and writing detection rules with **Sigma**. The workflow was based on a simulated SOC pipeline: sandbox analysis → detection → blocking → alerting.

---

## 1. First sample (sample1.exe)
After starting the machine, I opened the mailbox and saw an email with an attachment: **sample1.exe**.  
I uploaded the file into the **Malware Sandbox** and got its **SHA256 hash**.  

[Screenshot 01: Sandbox analysis of sample1.exe](./screenshots/01-sample1.png)

Then I copied the hash, went to **Manage Hashes**, pasted it, selected SHA256, and submitted.  
This triggered a new email with another sample and the first flag.  

[Screenshot 02: First flag and new sample](./screenshots/02-flag1.png)

---

## 2. Second sample – malicious HTTP requests
The second file was also analyzed in the sandbox. At the bottom of the report, I found suspicious **HTTP requests**.  

[Screenshot 03: Malicious HTTP request](./screenshots/03-http.png)

To mitigate this, I went to **Firewall Manager** and created a rule:  
- Type: `Egress`  
- Source IP: `Any`  
- Destination IP: (from the sandbox analysis)  
- Action: `Deny`  

After saving, I received another email with a new flag and the next sample.  

[Screenshot 04: Second flag](./screenshots/04-flag2.png)

---

## 3. Third sample – DNS requests
Analyzing the next sample showed suspicious **DNS requests** for a strange domain.  

[Screenshot 05: Suspicious DNS domain](./screenshots/05-dns.png)

I created a new rule in **DNS Filter**:  
- Rule name: descriptive (any name works)  
- Category: `Malware`  
- Domain name: suspicious domain from the sandbox  
- Action: `Deny`  

After that, another email arrived with the flag and next task.  

[Screenshot 06: Third flag](./screenshots/06-flag3.png)

---

## 4. Fourth sample – Registry activity
This sample modified the **Registry** to disable Windows Defender Real-Time Protection.  

[Screenshot 07: Registry modification](./screenshots/07-registry.png)

I created a Sigma rule in **Sigma Rule Builder**:  
- Logs: `Sysmon event logs`  
- Event type: `Registry Modifications`  
- Registry key: (from sandbox analysis)  
- Value: (from screenshot)  
- ATT&CK ID: `Defense Evasion`  

Submitting the rule triggered another email with the next flag.  

[Screenshot 08: Fourth flag](./screenshots/08-flag4.png)

---

## 5. Fifth sample – outgoing connections
This time I received a **log file** of outgoing connections.  
It showed one source communicating very frequently with the same IP, always with the same byte size.  

[Screenshot 09: Outgoing connections log](./screenshots/09-connections.png)

I created another Sigma rule:  
- Logs: `Sysmon Event Logs` → `Network Connections`  
- Remote IP: `Any`  
- Remote Port: `Any`  
- Size: `97`  
- Frequency: `1800`  
- ATT&CK ID: `Command and Control`  

That gave me the next email with another task.  

[Screenshot 10: Fifth flag](./screenshots/10-flag5.png)

---

## 6. Sixth sample – suspicious file creation
The last file contained **command logs**, showing multiple commands being saved into a suspicious file.  

[Screenshot 11: Suspicious command logging](./screenshots/11-commands.png)

I created the final Sigma rule:  
- Logs: `Sysmon Event Logs` → `File Creation and Modification`  
- File Path: `%temp%`  
- File Name: `exfiltr8.log`  
- ATT&CK ID: `Exfiltration`  

This completed the challenge and revealed the final flag.  

[Screenshot 12: Final flag](./screenshots/12-final-flag.png)

---

## Summary
In this room I practiced:
- **Malware sandboxing** to extract IoCs (hashes, domains, IPs).  
- Blocking malicious activity via **firewall rules** and **DNS filtering**.  
- Writing **Sigma detection rules** for Registry, network, and file events.  
- Mapping detections to **MITRE ATT&CK techniques** (Defense Evasion, Command and Control, Exfiltration).  

This was a complete end-to-end SOC simulation: from initial infection to creating effective detection and response rules.
