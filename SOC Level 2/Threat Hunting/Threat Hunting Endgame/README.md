# TryHackMe Room: Threat Hunting Endgame

This room was the culmination of the threat hunting module, challenging me to apply various techniques to investigate "Actions on Objectives." I explored how adversaries collect data, exfiltrate it from the network, and cause impact (like destroying backups), all while hunting through ELK logs.

---

## Section: Getting Started in Threat Hunting for "Actions on Objectives"

### Task 1: What is the term used for the adversary lifetime in the network?

The introduction explains the importance of proactive hunting to identify threats before they cause damage. The period where an adversary remains in a system unnoticed is technically referred to as Dwell Time.

---

## Section: Tactic: Collection

### Task 1: What is the Process ID of the process that downloads the malicious script?

To find the malicious activity, I searched the logs for the specific script or database file mentioned in the scenario.

*chrome-update_api.ps1* or *chrome_local_profile.db*

I sorted by time to find the earliest event. The field winlog.process.pid revealed the Process ID responsible for the download.

[Screenshot 01: PID of the malicious download process](./screenshots/01-download-pid.png)

The PID was 3388.

### Task 2: What is the logged mail account?

To understand the context of the script execution, I added specific fields to my Kibana view: winlog.event_data.ScriptBlockText, winlog.event_data.Path, and winlog.event_data.Payload.

Following the hint, I looked for an event containing the cat command. Once found, I used the "View surrounding documents" feature to see the logs immediately preceding and following that event. This revealed a command logging into a specific gmail.com account.

[Screenshot 02: Logged email account from surrounding events](./screenshots/02-email-account.png)

---

## Section: Tactic: Exfiltration

### Task 1: What is the total number of sent ICMP packets?

The attacker used ICMP (Ping) for exfiltration. I switched the index to case_exfiltration and used the search query provided in the instructions to find .NET network information usage:

*System.Net.Networkinformation.ping*

The search returned a hit showing the script loop. Analyzing the loop counter or the number of related events revealed the total packet count was 21.

[Screenshot 03: Count of ICMP packets](./screenshots/03-icmp-packet-count.png)

### Task 2: How many bytes (chunk) is the amount of data carried in each packet?

I examined the PowerShell script block in the logs (specifically the second event from the top in my search results). I found a variable definition: $readChunkSize = 15. This indicated the data chunk size per packet.

[Screenshot 04: Data chunk size variable](./screenshots/04-chunk-size.png)

### Task 3: What is the name of the exfiltrated document?

I searched for the exfiltration script *icmp4data.ps1* and examined the ScriptBlockText. The script arguments clearly showed the source file being read and sent over ICMP.

The file was chrome_local_profile.db.

[Screenshot 05: Exfiltrated file name and destination IP](./screenshots/05-exfil-file-ip.png)

### Task 4: What is the server's IP address (defanged) where the exfiltrated document is sent?

In the same script block found in the previous task (visible in Screenshot 05), the destination IP address for the ICMP packets was clearly defined as the target.

---

## Section: Tactic Impact

### Task 1: What is the name of the system executable used to remove shadow copies?

To investigate the impact phase, I focused on the process identified in the instructions (PID 1972). I searched for this ProcessId and added columns for CommandLine, ParentProcessName, and ParentImage to trace the activity.

winlog.event_data.ProcessId : "1972"

The logs showed that vssadmin.exe was executed, which is a standard Windows tool often abused by ransomware to delete shadow volume copies.

[Screenshot 06: vssadmin execution](./screenshots/06-vssadmin-execution.png)

### Task 2: What is the main shell image that started the attack chain?

Looking at the ParentImage or ParentProcessName field in the logs from the previous task, I could see that vssadmin.exe was spawned by PowerShell.exe.

### Task 3: What is the process ID that started the attack chain?

The ParentProcessId field associated with the execution of vssadmin.exe revealed the PID of the PowerShell process that initiated this part of the attack chain.

The PID was 6512.

---

# Summary

This endgame challenge tied together the concepts of Collection, Exfiltration, and Impact. I successfully:

- Hunted for Collection: Identified scripts used to gather local browser profiles.
- Analyzed Exfiltration: Dissected a custom PowerShell script used to tunnel data via ICMP packets.
- Investigated Impact: Traced process hierarchies to identify the destruction of shadow copies using vssadmin, a common tactic in ransomware attacks.

This room emphasized the importance of looking not just at what a process is (like PowerShell), but what arguments it is running and who spawned it.