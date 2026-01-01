# TryHackMe Room: Sighunt

This room focuses on Threat Detection using Sigma rules. The goal is to write specific detection rules based on behavior to flag malicious activities within the provided log data. It's a great exercise in understanding syntax and identifying Indicators of Compromise (IOCs).

---
## Setup

Before diving into the challenges, we need a unique identifier for our rules. I generated a UUID to use in the id field for all subsequent Sigma rules.

[Screenshot 01: Generated UUID](./screenshots/01-uuid.png)

---

### Challenge #1

Task: Detect HTA payload execution spawned by a browser.

For this challenge, I monitored for mshta.exe being spawned by Google Chrome, which is a common technique for drive-by downloads or malicious HTA file execution.

title: sighunt_room
id: f5167360-bfef-4245-b89b-686596a4703f
status: rule testing 
description: HTA payload detection
author: olsen
date: 01/10/25
modified: 01/10/25
logsource:
  product: windows
  service: process_creation
detection:
  selection:
    EventID: 1 
    ParentImage|endswith: '\chrome.exe'
    Image|endswith: '\mshta.exe'
  condition: selection

[Screenshot 02: Challenge 1 Flag](./screenshots/02-flag-1.png)

---

### Challenge #2

Task: Detect file downloads using Certutil.

Certutil is a classic "Living off the Land" binary. I wrote a rule to detect the specific flags (-urlcache, -split, -f) used to download files from the internet.

title: sighunt_room
id: f5167360-bfef-4245-b89b-686596a4703f
status: rule testing 
description: certutil download
author: olsen
date: 01/10/25
modified: 01/10/25
logsource:
  product: windows
  service: process_creation
detection:
  selection:
    EventID: 1 
    Image|endswith: '\certutil.exe'
    CommandLine|contains: 
      - ' -urlcache '
      - ' -split '
      - ' -f '
  condition: selection

[Screenshot 03: Challenge 2 Flag](./screenshots/03-flag-2.png)

---

### Challenge #3

Task: Detect Netcat execution.

Here, I looked for nc.exe specifically using the -e flag (which allows command execution upon connection). I also included a hash detection as a secondary selection method.

title: sighunt_room
id: f5167360-bfef-4245-b89b-686596a4703f
status: rule testing 
description: Netcat Execution
author: olsen
date: 01/10/25
modified: 01/10/25
logsource:
  product: windows
  service: process_creation
detection:
  selection1:
    EventID: 1 
    Image|endswith: '\nc.exe'
    CommandLine|contains|all: 
      - ' -e '
  selection2:
    Hashes|contains: '523613A7B9DFA398CBD5EBD2DD0F4F38'
  condition: selection1 or selection2

[Screenshot 04: Challenge 3 Flag](./screenshots/04-flag-3.png)

---

### Challenge #4

Task: Detect PowerUp enumeration.

This rule focuses on PowerShell executing the Invoke-AllChecks command, which is a signature function of the PowerUp privilege escalation tool.

title: sighunt_room
id: f5167360-bfef-4245-b89b-686596a4703f
status: rule testing 
description: PowerUp Enumeration
author: olsen
date: 01/10/25
modified: 01/10/25
logsource:
  product: windows
  service: process_creation
detection:
  selection:
    EventID: 1 
    Image|endswith: '\powershell.exe'
    CommandLine|contains: 
      - 'Invoke-AllChecks'
      - 'PowerUp'
  condition: selection

[Screenshot 05: Challenge 4 Flag](./screenshots/05-flag-4.png)

---

### Challenge #5

Task: Detect Service Binary modification.

Attackers often modify existing services to point to malicious executables. I wrote a rule to detect sc.exe (Service Control) changing the binPath configuration.

title: sighunt_room
id: f5167360-bfef-4245-b89b-686596a4703f
status: rule testing 
description: Service Binary
author: olsen
date: 01/10/25
modified: 01/10/25
logsource:
  product: windows
  service: process_creation
detection:
  selection:
    EventID: 1 
    Image|endswith: '\sc.exe'
    CommandLine|contains|all: 
      - ' config '
      - ' binPath= '
      - ' -e '
  condition: selection

[Screenshot 06: Challenge 5 Flag](./screenshots/06-flag-5.png)

---

### Challenge #6

Task: Detect RunOnce Persistence.

This rule detects the use of reg.exe to add an entry to the RunOnce registry key, a common method for establishing persistence.

title: sighunt_room
id: f5167360-bfef-4245-b89b-686596a4703f
status: rule testing 
description: RunOnce Persistence
author: olsen
date: 01/10/25
modified: 01/10/25
logsource:
  product: windows
  service: process_creation
detection:
  selection:
    EventID: 1 
    Image|endswith: '\reg.exe'
    CommandLine|contains: 
      - ' add '
      - ' RunOnce '
  condition: selection

[Screenshot 07: Challenge 6 Flag](./screenshots/07-flag-6.png)

---

### Challenge #7

Task: Detect password-protected archive creation.

Attackers often archive data with a password before exfiltrating it to bypass DLP systems. I looked for 7z.exe using the -p (password) flag.

title: sighunt_room
id: f5167360-bfef-4245-b89b-686596a4703f
status: rule testing 
description: 7z Archive
author: olsen
date: 01/10/25
modified: 01/10/25
logsource:
  product: windows
  service: process_creation
detection:
  selection:
    EventID: 1 
    Image|endswith: '\7z.exe'
    CommandLine|contains: 
      - ' a '
      - ' -p '
  condition: selection

[Screenshot 08: Challenge 7 Flag](./screenshots/08-flag-7.png)

---

### Challenge #8

Task: Detect Curl data exfiltration.

This rule detects the use of curl.exe with the -d flag, which is used to send data (often via POST) to a remote server.

title: sighunt_room
id: f5167360-bfef-4245-b89b-686596a4703f
status: rule testing 
description: Curl Data
author: olsen
date: 01/10/25
modified: 01/10/25
logsource:
  product: windows
  service: process_creation
detection:
  selection:
    EventID: 1 
    Image|endswith: '\curl.exe'
    CommandLine|contains: 
      - ' -d '
  condition: selection

[Screenshot 09: Challenge 8 Flag](./screenshots/09-flag-8.png)

---

### Challenge #9

Task: Detect Ransomware file creation.

Finally, I monitored specifically for Sysmon Event ID 11 (File Creation) where the created file ends with the suspicious extension .huntme.

title: sighunt_room
id: f5167360-bfef-4245-b89b-686596a4703f
status: rule testing 
description: Ransomware File
author: olsen
date: 01/10/25
modified: 01/10/25
logsource:
  product: windows
  service: process_creation
detection:
  selection:
    EventID: 11
    TargetFilename:  '*.huntme'
  condition: selection

[Screenshot 10: Challenge 9 Flag](./screenshots/10-flag-9.png)

---

# Summary

In this room, I practiced:

- Generating UUIDs for Sigma rule tracking.
- Writing Sigma rules to detect various stages of the Cyber Kill Chain:
  - Execution (HTA, Netcat, Service Binary).
  - Defense Evasion (Certutil, RunOnce).
  - Discovery (PowerUp).
  - Exfiltration (7zip encryption, Curl).
  - Impact (Ransomware file creation).
- Translating malicious behavior into logical selection fields within a YAML structure.

