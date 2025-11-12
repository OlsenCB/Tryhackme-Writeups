# TryHackMe Room: Boogeyman 3

This room was the final chapter in the Boogeyman series, providing a deep dive into centralized log analysis. Instead of relying on host-based forensics, I used Elasticsearch and Kibana to hunt for attacker activity by analyzing winlogbeat logs. The goal was to reconstruct the entire attack chain from the initial infection to lateral movement and privilege escalation.

---

## The Chaos Inside

### Task 1: What is the PID of the process that executed the initial stage 1 payload?

To begin, I opened the provided Kibana dashboard and set the index pattern to winlogbeat. I adjusted the timeline to cover the relevant period: August 29 to August 30, 2023. I then used the search bar to find logs related to the first-stage payload, ProjectFinancialSummary_Q3.pdf. I quickly learned that the .pdf was actually a decoy, and the file was an HTA (HTML Application), a file format often abused to execute malicious code.

I set the following fields to be visible: process.command_line, process.parent.command_line, process.name, and process.pid. My search query was *Project*. The results showed the mshta.exe process with the PID that executed the initial payload.

[Screenshot 01: PID of the initial payload process](./screenshots/01-process-pid.png)

### Task 2: The stage 1 payload attempted to implant a file to another location. What is the full command-line value of this execution?

To follow the chain of events, I clicked on the event and selected "View surrounding documents." This showed events immediately before and after the mshta.exe execution. I found that xcopy.exe was used to copy a file named review.dat to a new location. This command was the answer.

[Screenshot 02: Surrounding documents (xcopy command)](./screenshots/02-surrounding-documents.png)

### Task 3: The implanted file was eventually used and executed by the stage 1 payload. What is the full command-line value of this execution?

Continuing to follow the timeline, I saw that the review.dat file was later executed using rundll32.exe. This command was a crucial step in the attack chain.

### Task 4: The stage 1 payload established a persistence mechanism. What is the name of the scheduled task created by the malicious script?

My timeline showed a PowerShell command that created a scheduled task. By analyzing the command, I identified the persistence mechanism. The Register-ScheduledTask cmdlet was used to create a task named Review that would run daily at 6:00 AM.

### Task 5: The execution of the implanted file inside the machine has initiated a potential C2 connection. What is the IP and port used by this connection? (format: IP:port)

I shifted my focus to network connections by searching for event.code: 3, which corresponds to network connection events in the logs. I added the destination.ip and destination.port fields. The logs showed numerous connections to a specific IP address and port, indicating a C2 communication channel.

[Screenshot 03: C2 IP and port](./screenshots/03-c2-ip-port.png)

### Task 6: The attacker has discovered that the current access is a local administrator. What is the name of the process used by the attacker to execute a UAC bypass?

UAC (User Account Control) is a Windows security feature that helps prevent unauthorized changes to the system. A UAC bypass allows an attacker to elevate their privileges to a full administrator without being prompted. I filtered the logs to find events related to the review.dat file and looked at the process command lines. I found a process named fodhelper.exe that was executed by review.dat, which is a known technique for UAC bypass.

[Screenshot 04: UAC bypass process name](./screenshots/04-uac-bypass.png)

### Task 7: Having a high privilege machine access, the attacker attempted to dump the credentials inside the machine. What is the GitHub link used by the attacker to download a tool for credential dumping?

With elevated privileges, the attacker moved to dump credentials. I searched the logs for anything related to GitHub and quickly found a log entry showing the download of a mimikatz tool from a GitHub repository.

[Screenshot 05: GitHub link to credential dumping tool](./screenshots/05-github-link.png)

### Task 8: After successfully dumping the credentials inside the machine, the attacker used the credentials to gain access to another machine. What is the username and hash of the new credential pair? (format: username:hash)

I filtered the logs for keywords related to credential dumping, such as *sekurlsa*. The logs revealed a clear log entry showing a dumped credential pair, including a username and hash.

[Screenshot 06: Dumped ITAdmin credentials](./screenshots/06-itadmin-dumped.png)

### Task 9: Using the new credentials, the attacker attempted to enumerate accessible file shares. What is the name of the file accessed by the attacker from a remote share?

The logs showed the attacker using PowerView.ps1, a tool for network enumeration. I searched for this file and found an entry for Invoke-ShareFinder. Following this lead, I found a log showing access to a remote share and the name of a file within it.

[Screenshot 07: File from remote share](./screenshots/07-remote-share-file.png)

### Task 10: After getting the contents of the remote file, the attacker used the new credentials to move laterally. What is the new set of credentials discovered by the attacker? (format: username:password)

I searched for the keyword PSCredential to find the logs related to the discovery of new credentials. The logs revealed a new username and password pair used for lateral movement.

[Screenshot 08: New PSCredential credentials](./screenshots/08-pscredential.png)

### Task 11: What is the hostname of the attacker's target machine for its lateral movement attempt?

The command that set the new credentials showed a -ComputerName parameter with the hostname of the new target machine.

### Task 12: Using the malicious command executed by the attacker from the first machine to move laterally, what is the parent process name of the malicious command executed on the second compromised machine?

I shifted my search to the new target machine, filtering for event.code: 1 (process creation) and the target hostname WKSTN-1327. I found a suspicious whoami.exe process with a parent process that was the answer.

[Screenshot 09: Parent process of whoami.exe](./screenshots/09-whoami-parent.png)

### Task 13: The attacker then dumped the hashes in this second machine. What is the username and hash of the newly dumped credentials? (format: username:hash)

Scrolling through the logs, I found a new log entry for credential dumping, this time for the administrator account on the second machine.

[Screenshot 10: Dumped Administrator credentials](./screenshots/10-administrator-dumped.png)

### Task 14: After gaining access to the domain controller, the attacker attempted to dump the hashes via a DCSync attack. Aside from the administrator account, what account did the attacker dump?

After finding the administrator credentials in the previous task, I suspected the attacker would use them for lateral movement to the domain controller. I attempted to filter the logs by host.hostname is DC01.

This filter proved to be both a hit and a miss. While it didn't immediately give me the answer I was looking for, it led to an unexpected discovery. I saw an event showing the itadmin user downloading a file named ransomboogey.exe. As it turned out, this was the answer to a future question about the downloaded ransomware file. I clicked on the event and found the URL from which it was downloaded.

[Screenshot 11: Ransomware download URL](./screenshots/11-ransomware-url.png)

But let's return to the current question. Since that filter was not successful for this specific task, and knowing that this is a DCSync attack, I simply searched for DCSync in the logs.

[Screenshot 12: DCSync log entries](./screenshots/12-dcsync-log.png)

The search results confirmed my suspicion. We see a reference to user:administrator as well as another user: user:backupda. Since the question specifically asks for an account other than the administrator, backupda is our answer.

---

## Summary
This final room in the Boogeyman series was an incredible exercise in threat hunting and incident response using a centralized logging platform. I learned how to:

Leverage Elasticsearch and Kibana to efficiently analyze massive amounts of data.

Correlate log events to follow the attacker's TTPs from initial access to privilege escalation and lateral movement.

Identify key attacker tools such as PowerShell for persistence, mimikatz for credential dumping, and PowerView for network enumeration.

Reconstruct an entire attack chain by piecing together fragmented evidence from system logs.

This process is a fundamental skill for any security professional, as it allows for a comprehensive understanding of an attack, far beyond what is possible with host-based analysis alone.





