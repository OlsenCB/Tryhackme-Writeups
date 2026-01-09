# TryHackMe Room: Threat Hunting: Foothold

This room focused on the practical application of threat hunting within an ELK (Elasticsearch, Logstash, Kibana) environment. I was tasked with identifying the initial foothold of an attacker, tracing their execution path, uncovering defense evasion techniques, identifying persistence mechanisms, and finally, analyzing Command and Control (C2) traffic.

---

## Section: Initial Access

### Task 1: What is the attacker's successful authentication timestamp on the Jumphost server?

I started by accessing the Discover tab in Kibana and selecting the filebeat-* index pattern. The goal was to find a successful SSH login on the jump host. I constructed a query to filter for the specific hostname, authentication category, and the "Accepted" event status.

host.name: jumphost AND event.category: authentication AND system.auth.ssh.event: Accepted

Expanding the relevant event revealed the timestamp.

[Screenshot 01: SSH Authentication Timestamp](./screenshots/01-ssh-timestamp.png)

### Task 2: What is the name of the PHP file accessed by the attacker via the cat command after gaining successful code execution on web01?

For this task, I focused on the web01 host. Based on the room's context, I knew the attacker's source IP was 167.71.198.43. I filtered for successful HTTP responses (200) and looked for a URL query containing the cat command, which indicates command injection or webshell usage.

host.name: "web01" and source.ip: 167.71.198.43 and http.response.status_code: 200 and url.query: *cat*

[Screenshot 02: Cat command via Webshell](./screenshots/02-cat-command.png)

### Task 3: What is the name of the unusual process executed within the timeframe of update.lnk execution on WKSTN-2?

Moving to the Windows workstation (WKSTN-2), I switched the index pattern to winlogbeat-*. I knew the malicious file was likely update.lnk contained within a zip file. I searched for the zip file access first.

host.name: WKSTN-* AND *Update.zip*

After locating the event triggered by Explorer.EXE, I used the "View surrounding documents" feature to see what processes started immediately after. This revealed the unusual process execution.

[Screenshot 03: Unusual process near Update.zip](./screenshots/03-unusual-process.png)

---

## Section: Execution

### Task 1: Tracing back the cmd and PowerShell child processes spawned by installer.exe, what is the first command executed via cmd?

Staying in the winlogbeat-* index, I investigated the installer.exe process. I searched for it and examined the process.command_line field to see exactly what arguments were passed to cmd.exe.

"installer.exe"

[Screenshot 04: Installer.exe command line](./screenshots/04-installer-cmdline.png)

### Task 2: Using the process ID of the PowerShell process spawned by mshta.exe, what is the destination IP of the network connections made by this process?

Based on the investigation flow and the previous task's logs, the destination IP for the network connection was identified as 167.71.198.43.

### Task 3: Following the cmd.exe process spawned by Python, what is the command-line value of the net.exe process?

To find this specific execution, I used a targeted query filtering for Process Creation events (Event ID 1 or 3) and the known parent PID (1832).

host.name: WKSTN-* AND winlog.event_id: (1 OR 3) AND process.parent.pid: 1832

I added process.name and process.command_line to the view, which highlighted the single event related to net.exe.

[Screenshot 05: Net.exe process details](./screenshots/05-net-exe.png)

---

## Section: Defense Evasion

### Task 1: What is the PID of the cmd.exe process that executed "powershell Set-MpPreference -DisableRealtimeMonitoring $true"?

I searched for keywords related to disabling Windows Defender.

host.name: WKSTN-* AND (*DisableRealtimeMonitoring* OR *RemoveDefinitions*)

I inspected the process.pid and process.parent.command_line fields of the resulting events to identify the PID of the command prompt responsible for this action.

[Screenshot 06: PID of cmd disabling Defender](./screenshots/06-defender-disable-pid.png)

### Task 2: What is the PowerShell command-line argument used to clear the event logs of WKSTN-1?

I knew that the standard PowerShell command for clearing logs is Clear-EventLog. I searched for this phrase to find the exact arguments used by the attacker.

*Clear-EventLog*

[Screenshot 07: Clear-EventLog command](./screenshots/07-clear-eventlog.png)

### Task 3: What is the process PID of chrome.exe's target for process injection?

The attacker attempted to inject code into explorer.exe using chrome.exe. I constructed a query to find events on the workstation involving explorer.exe and looked specifically at the TargetImage field in the event data.

host.name: WKSTN-* AND "C\Windows\explorer.exe"

I filtered to ensure explorer.exe was the TargetImage and checked the TargetProcessId to answer the question.

[Screenshot 08: Target Process ID for injection](./screenshots/08-target-pid.png)

---

## Section: Persistence

### Task 1: What is the name of the parent process of the cmd.exe process that executed the scheduled task creation?

I searched for Event ID 4698 (Scheduled Task Creation) or keywords like schtasks or Register-ScheduledTask.

host.name: WKSTN-* AND (winlog.event_id: 4698 OR (*schtasks* OR *Register-ScheduledTask*))

By adding process.parent.name and process.parent.command_line to the columns, I identified the parent process responsible for creating the persistence mechanism.

[Screenshot 09: Parent process of scheduled task creation](./screenshots/09-schtasks-parent.png)

### Task 2: Using the process ID of malicious reg.exe execution, what is the value of the process command line used to execute the registry modification?

Following the previous steps, I identified the PID for the malicious reg.exe execution was 5860. I searched for this PID and examined the process.command_line to see the exact registry modification command.

[Screenshot 10: Registry modification command line](./screenshots/10-reg-cmdline.png)

---

## Section: Command and Control

### Task 1: What is the link downloaded using PowerShell to establish the C2 over DNS?

From the previous analysis of the registry command line (Screenshot 10), I could clearly see a PowerShell command downloading a script from GitHub to establish a DNS-based C2 channel. The link pointed to dnscat2.ps1.

### Task 2: After investigating C2 over Discord events, what command is used to download the malicious dev.py python script?

I traced the activity back to the installer.exe process found in the Windows Temp folder.

host.name: WKSTN-1* AND winlog.event_id: 1 AND process.parent.executable: "C:\\Windows\\Temp\\installer.exe"

Checking the process.parent.command_line revealed the command used to fetch the python script.

[Screenshot 11: Dev.py download command](./screenshots/11-dev-py-download.png)

### Task 3: What is the name of the process that is also associated with cdn[.]golge[.]xyz?

I performed a broad search for the suspicious domain cdn.golge.xyz.

host.name: WKSTN-* AND *cdn.golge.xyz*

I checked the process.executable and process.name fields in the search results to find the associated binary.

[Screenshot 12: Process associated with C2 domain](./screenshots/12-c2-process.png)

---

# Summary

This room was a comprehensive exercise in Threat Hunting, covering the entire attack lifecycle. I learned to:

- Switch Contexts: Move between filebeat (Linux/SSH) and winlogbeat (Windows) indices based on the target host.
- Trace Process Trees: Use Parent PIDs and "surrounding documents" to reconstruct the chain of execution from installer.exe to cmd and PowerShell.
- Identify Evasion: Spot attempts to disable Defender and clear event logs.
- Uncover Persistence: Find scheduled tasks and registry modifications used to maintain access.
- Analyze C2: Decode command lines to find external C2 domains and scripts (DNSCat2, Discord C2).