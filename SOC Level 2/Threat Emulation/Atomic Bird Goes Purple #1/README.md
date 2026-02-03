# TryHackMe Room: Atomic Bird Goes Purple #1

This room focuses on Purple Teaming concepts using the Atomic Red Team framework. I stepped into the shoes of both the attacker (Red) by executing specific atomic tests and the defender (Blue) by investigating the generated artifacts and logs to detect the activity.

---

## Section: Toolset and Hints

### Task 1: Use the required PowerShell command to retrieve the flag. What is the flag?

The room instructions mentioned a set of custom utilities available in the machine. To retrieve the initial flag, I used the provided THM-Utils command in PowerShell.

THM-LogStats-Flag

[Screenshot 01: THM-LogStats-Flag output](./screenshots/01-logstats-flag.png)

### Task 2: What is the required command to clear all generated artefacts and restore the affected files from test T0123-4?

Based on my previous experience with Atomic Red Team, I knew that to reverse the effects of a test, we simply need to append the -Cleanup flag to the execution command.

Answer: Invoke-AtomicTest T0123–4 -Cleanup

---

## Section: Execute, Investigate, Detect

### Task 1: Execute test T0004-1 and open the document created on the Desktop. What is the OS Build info?

I started the active testing phase by running the first atomic test.

Invoke-AtomicTest T0004-1

After execution, a new document appeared on the Desktop. I opened it and found the OS Build information inside.

[Screenshot 02: OS Build info from Desktop file](./screenshots/02-os-build-info.png)

### Task 2: Execute test T0004-2. What is the flag?

I proceeded to the next test case.

Invoke-AtomicTest T0004-2

Upon execution, a pop-up window appeared on the screen containing the flag.

[Screenshot 03: Popup window with flag](./screenshots/03-popup-flag.png)

### Task 3: Execute test T0004-3. Examine the logs; what is the failed command?

To understand what this specific test attempts to do (and why it might fail or what command triggers the failure), I checked the details of the test configuration.

Invoke-AtomicTest T0004-3 -ShowDetails

[Screenshot 04: T0004-3 failed command details](./screenshots/04-failed-command.png)

---

## Section: Universal Suspicious Share

### Task 1: Navigate the disk and drives, and open the shared folder. What is the SHA256 value of the ".txt" document?

I navigated to the mapped S: drive to inspect the Donation_call.txt file. To get the SHA256 hash, I used the Get-FileHash cmdlet.

Get-FileHash -Path S:\Donation_call.txt -Algorithm SHA256

[Screenshot 05: Original SHA256 hash](./screenshots/05-original-hash.png)

### Task 2: Execute the test T0005-1. Re-calculate the SHA256 value of the document. What is the hash value?

I ran the atomic test which modifies the file.

Invoke_AtomicTest T0005-1

After the test completed, I recalculated the hash to observe the change.

Get-FileHash -Path S:\Donation_call.txt -Algorithm SHA256

[Screenshot 06: Modified SHA256 hash](./screenshots/06-modified-hash.png)

---

## Section: Dump and Go

### Task 1: Execute test T0006-1. Find the malicious history dump file. What is the flag?

Before starting, I cleared the existing logs using THM-LogClear-All to ensure a clean slate for investigation. Then, I executed the test.

Invoke_AtomicTest T0006-1

I opened the Event Viewer and checked the Security logs. I looked for Event ID 4663 (An attempt was made to access an object). I noticed a process accessing a file named analytics.txt. When I opened that file, I found the flag.

[Screenshot 07: Analytics.txt content](./screenshots/07-analytics-flag.png)

### Task 2: Execute test T0006-2. Find the malicious system file modification activity. What is the flag?

I ran the second test in this sequence.

Invoke-AtomicTest T0006-2

Returning to the Security logs in Event Viewer, I searched for Event ID 4663 again to identify which file was targeted. The logs showed access to the hosts file in the /etc/ directory (or Windows equivalent drivers\etc).

[Screenshot 08: Event ID 4663 targeting hosts file](./screenshots/08-event-4663-hosts.png)

I opened the hosts file to inspect the modifications and found the flag.

(Screenshot 09)

---

# Summary

This room was a great practical exercise in Purple Teaming. I learned how to:

- Execute Attacks: Use the Invoke-AtomicTest framework to simulate specific threat behaviors.
- Validate Integrity: Use file hashing (Get-FileHash) to detect unauthorized changes to files on shared drives.
- Investigate Logs: Use Windows Event Viewer (specifically Security Event ID 4663) to trace process activity and identify files accessed or modified during an attack.