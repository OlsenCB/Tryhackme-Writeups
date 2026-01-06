# TryHackMe Room: Threat Hunting: Pivoting

In this room, I continued my journey through the Threat Hunting module. The focus here was on Pivoting—the art of using one piece of evidence (like a suspicious process) to uncover another (like a compromised user account or infected host). I used the ELK stack to trace an attacker moving through the network, escalating privileges, and dumping credentials.

---

## Section: Discovery

### Task 1: What is the name of the account seen to be executing host enumeration commands on DC01?

I started by hunting for standard enumeration activity. I used the KQL query provided in the room instructions, which filters for common Windows reconnaissance tools like whoami, net, and systeminfo.

winlog.event_id: 1 AND process.name: (whoami.exe OR hostname.exe OR net.exe OR systeminfo.exe OR ipconfig.exe OR netstat.exe OR tasklist.exe)

To make the analysis easier, I added the columns user.name, host.name, process.parent.command_line, and process.command_line to the view. This immediately revealed the user account running these commands.

[Screenshot 01: Username executing enumeration commands](./screenshots/01-enumeration-user.png)

### Task 2: Following the port scanning activity investigation, what is the parent process of n.exe?

In the logs, I noticed a suspicious binary named n.exe (likely a renamed Nmap). To find its origin, I searched for n.exe and checked the process.parent.command_line field.

[Screenshot 02: Parent process of n.exe](./screenshots/02-parent-process-n.png)

### Task 3: What is the full command-line value of the SharpHound.exe process?

Searching specifically for SharpHound.exe (a known BloodHound collector), I examined the process.command_line field to see exactly what arguments were passed to it.

[Screenshot 03: SharpHound command line](./screenshots/03-sharphound-cmd.png)

---

## Section: Privilege Escalation

### Task 1: What is the full command-line value of the process spawned by spoofer.exe?

I searched for the binary spoofer.exe. By looking at the process tree or searching for processes where the parent was spoofer.exe, I found the child process and its full command line.

[Screenshot 04: Process spawned by spoofer.exe](./screenshots/04-spoofer-child.png)

### Task 2: What is the name of the other service that was abused besides SNMPTRAP?

The attacker used a registry modification to achieve persistence via services. I used the provided query to look for Event ID 13 (Registry Value Set) where the ImagePath was modified to point to a suspicious update.exe.

winlog.event_id: 13 AND registry.path: *HKLM\\System\\CurrentControlSet\\Services\\*\\ImagePath*

The results showed modifications to SNMPTRAP and another service: Spooler.

[Screenshot 05: Abused Spooler service in registry](./screenshots/05-spooler-service.png)

### Task 3: What is the MD5 hash of the update.exe binary?

To identify the malicious binary update.exe masquerading as a service, I searched for its execution (Event ID 1) where the parent was services.exe.

winlog.event_id: 1 AND process.parent.name: services.exe AND process.name: update.exe

I checked the hash.md5 field in the event details to get the hash.

[Screenshot 06: MD5 hash of update.exe](./screenshots/06-update-exe-md5.png)

---

## Section: Credential Access

### Task 1: What is the name of the process that created the lsass.DMP file?

Dumping lsass.exe is a common technique to steal credentials. I searched for the creation of the file lsass.DMP and checked the process.name field to see which tool created it.

[Screenshot 07: Process name creating lsass.DMP](./screenshots/07-lsass-dmp-process.png)

### Task 2: Out of the four GUIDs used to hunt DCSync, what is the value of the GUID seen in the existing logs?

DCSync attacks can be detected by monitoring specific Access Rights (GUIDs) on the Domain Object. I used the complex query provided in the instructions, which filters for Event ID 4662 (Operation on an object) and specific access masks.

winlog.event_id: 4662 AND winlog.event_data.AccessMask: 0x100 AND winlog.event_data.Properties: (*1131f6aa-9c07-11d1-f79f-00c04fc2dcd2* OR *1131f6ad-9c07-11d1-f79f-00c04fc2dcd2* OR *9923a32a-3607-11d2-b9be-0000f87a36b2* OR *89e95b76-444d-4c62-991a-0facbeda640c*)

Looking at the winlog.event_data.Properties field in the result, I matched one of the GUIDs from the list.

[Screenshot 08: DCSync GUID found in logs](./screenshots/08-dcsync-guid.png)

### Task 3: What is the name of the first process spawned by jade.burke on WKSTN-1?

I pivoted to investigate the user jade.burke on the workstation WKSTN-1.

host.name: WKSTN-1* AND winlog.event_id: 1 AND user.name: jade.burke

Sorting by time, the first process spawned was whoami.exe.

[Screenshot 09: First process by jade.burke](./screenshots/09-jade-burke-first-process.png)

---

## Section: Lateral Movement

### Task 1: What is the name of the account that also used WMIExec on WKSTN-1 aside from clifford.miller?

WMI lateral movement often involves the WmiPrvSE.exe process spawning children. I used the provided query to find such events:

winlog.event_id: 1 AND process.parent.name: WmiPrvSE.exe

I added user.name, process.command_line, and process.parent.command_line to my columns. Reviewing the results, I saw that besides clifford.miller, the user jade.burke also initiated processes via WMI.

[Screenshot 10: Jade Burke using WMIExec](./screenshots/10-wmi-jade-burke.png)

### Task 2: Excluding the false positive account, how many events were generated by potential Pass-the-Hash authentications?

Pass-the-Hash (PtH) can often be detected by looking for Logon Type 3 (Network) using NTLM where the Key Length is 0.

winlog.event_id: 4624 AND winlog.event_data.LogonType: 3 AND winlog.event_data.LogonProcessName: *NtLmSsp* AND winlog.event_data.KeyLength: 0

I filtered the results to exclude the system noise (like ANONYMOUS LOGON). After removing those 2 false positives from the total hits, I had the answer.

[Screenshot 11: Pass-the-Hash event count](./screenshots/11-pth-count.png)

### Task 3: Excluding the executions of the cd command, what is the full command-line value of the subsequent process spawned after the first successful PtH authentication of clifford.miller?

I located the first successful PtH authentication event for clifford.miller from the previous task. To see what happened after the login, I selected that event and used the "View surrounding documents" feature.

I loaded the 20 succeeding documents and filtered for winlog.event_id: 1 (Process Creation). Ignoring the initial cd command, I found the next substantive command executed by the attacker.

[Screenshot 12: Command executed after PtH](./screenshots/12-post-pth-command.png)

---

Summary

This room was an excellent exercise in pivoting. Instead of just finding one alert and stopping, I used the artifacts from one stage (like a compromised user account) to query for activity in the next (like lateral movement or privilege escalation). Key techniques included:

- Registry Hunting: Finding malicious services by looking for ImagePath modifications.
- DCSync Detection: Using specific GUIDs in Active Directory access logs.
- Contextual Analysis: Using "Surrounding Documents" to correlate network logons (Event 4624) with immediate process execution (Event 1).
