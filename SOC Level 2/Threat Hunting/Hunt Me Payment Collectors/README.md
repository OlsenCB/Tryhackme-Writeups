# TryHackMe Room: Hunt Me I: Payment Collectors

This room is a dedicated threat hunting challenge where I was tasked with investigating a compromised host using the ELK Stack (Elasticsearch, Logstash, Kibana). The scenario involves a user named Michael who downloaded a suspicious attachment, leading to a compromise. My goal was to reconstruct the attack chain, from initial access to data exfiltration.

---

## Section: Scenario Investigation

### Task 1: What was the name of the ZIP attachment that Michael downloaded?

I started by setting the time range in Kibana to match the scope provided in the room description. To find the initial infection vector, I searched for any files with a .zip extension.

*.zip

The search results revealed the malicious attachment. I also noticed another zip file in the results, which I kept in mind as it might be relevant for later tasks regarding exfiltration.

[Screenshot 01: Initial ZIP file discovery](./screenshots/01-zip-discovery.png)

### Task 2: What was the contained file that Michael extracted from the attachment?

To see what happened after the zip was downloaded, I searched for the specific filename found in Task 1 and looked for file events occurring immediately after it was accessed.

*Invoice_AT_2023-227.zip*

The logs showed that extracting the zip archive revealed a shortcut (.lnk) file, which is a common technique used to mask malicious scripts.

[Screenshot 02: LNK file extracted from ZIP](./screenshots/02-lnk-file.png)

### Task 3: What was the name of the command-line process that spawned from the extracted file attachment?

I filtered for Process Creation events (Event ID 1). Looking at the timeline immediately following the execution of the .lnk file, I observed a PowerShell process being spawned. This process used powercat to establish a reverse shell.

[Screenshot 03: PowerShell spawning Powercat](./screenshots/03-powershell-powercat.png)

### Task 4: What URL did the attacker use to download a tool to establish a reverse shell connection?

Analyzing the command line arguments of the PowerShell process found in the previous task (visible in Screenshot 03), I could see the exact URL used to download the powercat tool.

### Task 5: What port did the workstation connect to the attacker on?

The same PowerShell command line argument revealed the IP address and the port used for the reverse connection.

### Task 6: What was the first native Windows binary the attacker ran for system enumeration after obtaining remote access?

Continuing to follow the process execution timeline (Event ID 1) after the reverse shell was established, I saw the attacker run a standard Windows binary to gather system information.

The binary was systeminfo.exe.

### Task 7: What is the URL of the script that the attacker downloads to enumerate the domain?

I dug deeper into the attacker's activities. While looking for further enumeration, I noticed they eventually mapped a network drive (Z:). However, before that, they likely used a script to understand the domain layout.

I switched my focus to PowerShell logs (Event ID 4104 - Script Block Logging). I searched for common enumeration keywords and found an event where PowerView.ps1 was downloaded. The command included the source URL from GitHub.

[Screenshot 05: PowerView download URL](./screenshots/05-powerview-url.png)

### Task 8: What was the name of the file share that the attacker mapped to Michael's workstation?

I searched for events related to network drive mapping (often involving net use or similar activities). The logs showed that the attacker mapped the Z: drive to a specific network share.

[Screenshot 04: Mapped network drive Z](./screenshots/04-mapped-drive.png)

### Task 9: What directory did the attacker copy the contents of the file share to?

After mapping the drive, the attacker used robocopy to move data. By analyzing the robocopy command arguments in the logs, I identified the local destination directory where the files were copied.

### Task 10: What was the name of the Excel file the attacker extracted from the file share?

By looking at the file access logs or the arguments within the robocopy event, I identified the specific .xlsx file that was targeted and copied from the shared drive.



### Task 11: What was the name of the archive file that the attacker created to prepare for exfiltration?

I searched for file creation events within the directory identified in Task 9. I found that the attacker compressed the stolen data into a new ZIP file to prepare it for exfiltration.

[Screenshot 06: Archive created for exfiltration](./screenshots/06-exfil-archive.png)

### Task 12: What is the MITRE ID of the technique that the attacker used to exfiltrate the data?

Analyzing the final stages of the attack, I observed the attacker using nslookup to send data to their server. This technique involves encoding data into DNS queries to bypass firewall restrictions.

This corresponds to the MITRE ATT&CK technique: Exfiltration Over Alternative Protocol (T1048).

[Screenshot 07: Nslookup command for exfiltration](./screenshots/07-nslookup-exfil.png)

### Task 13: What was the domain of the attacker's server that retrieved the exfiltrated data?

The domain was clearly visible in the nslookup commands used for the exfiltration.

### Task 14: The attacker exfiltrated an additional file from the victim's workstation. What is the flag you receive after reconstructing the file?

The nslookup queries contained long, random-looking subdomains. Recognizing this as Base64 encoded data, I took the string from the DNS query and decoded it using CyberChef. The decoded output revealed the flag.

[Screenshot 08: Decoded flag from CyberChef](./screenshots/08-decoded-flag.png)

---

# Summary

This room was a classic example of hunting through the Cyber Kill Chain using ELK. I tracked the attacker from:

1. Delivery/Exploitation: Malicious ZIP/LNK file.
2. C2: Establishing a reverse shell via Powercat.
3. Enumeration: Using systeminfo and PowerView.
4. Collection: Mapping a network share and copying sensitive Excel files.
5. Exfiltration: Using DNS tunneling (nslookup) to steal the data.