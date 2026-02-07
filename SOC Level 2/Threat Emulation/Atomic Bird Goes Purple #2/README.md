# TryHackMe Room: Atomic Bird Goes Purple #2

This room continues the Purple Teaming journey using the Atomic Red Team framework. In this session, I focused on intermediate techniques involving discovery, evasion, manipulation, and persistence mechanisms. I executed tests to modify system components and analyzed the resulting logs to detect these changes.

---

## Section: In-Between — Discover and Hide

### Task 1: Execute test T0002-1 and open the document created on the Desktop. Which PowerShell library file is detected?

I started by executing the first atomic test for this section.

Invoke-AtomicTest T0002-1

Once the test finished, I opened the newly created file on the Desktop. Inside, I found the name of the detected PowerShell library.

[Screenshot 01: PowerShell library in desktop file](./screenshots/01-library-file.png)

### Task 2: Now go to the atomics path and update the executed script to include all "bak" files. What is the code snippet that needs to be added to the code?

First, I cleaned up the previous test artifacts.

Invoke-AtomicTest T0002-1 -Cleanup

To find the script location, I viewed the test details.

Invoke-AtomicTest T0002-1 -ShowDetails

[Screenshot 02: Atomic test details](./screenshots/02-test-details.png)

The details pointed to cleartxt-scan.psd1. I opened this script and modified the Get-ChildItem command to include *.bak files.

[Screenshot 03: Modified script with *.bak](./screenshots/03-modified-script.png)

### Task 3: Run the cleanup command for the test T0002-1 and re-execute the test. Open the output file, and investigate the detected files. What is the secret key?

With the script modified, I executed the test again.

Invoke-AtomicTest T0002-1

I checked the output file on the Desktop again. This time, it listed a new file named old.bak. I navigated to that file and opened it to find the secret key.

[Screenshot 04: Output file showing old.bak](./screenshots/04-output-file.png)

[Screenshot 05: Secret key in old.bak](./screenshots/05-secret-key.png)

### Task 4: Execute test T0002-2 and investigate logs. What is the new account name?

Before running the test, I cleared the Security and Sysmon/Operational logs to ensure a clean investigation environment. Then, I executed the test.

Invoke-AtomicTest T0002-2

I opened the Event Viewer and checked the Security logs. I filtered for Event ID 4720 (A user account was created). The event details revealed the name of the newly created account.

[Screenshot 06: New user account in Security logs](./screenshots/06-new-user-account.png)

---

## Section: Manipulate, Deface, Persistence

### Task 1: Execute test T0003-1. What is the name of the created service?

I cleared the logs (Security and Sysmon) again before proceeding.

Invoke-AtomicTest T0003-1

I navigated to the Sysmon/Operational logs and searched for Event ID 13 (Registry Value Set). By examining the TargetObject field, I found the name of the service being modified.

[Screenshot 07: Target object registry key](./screenshots/07-target-object.png)

### Task 2: Which image is used to set the registry value for the created service?

Looking at the same event from the previous task, the Image field provided the answer.

### Task 3: Execute test T0003-2. What is the ransom note?

I cleared the logs and ran the next test.

Invoke-AtomicTest T0003-2

I checked the Sysmon/Operational logs for another Event ID 13 (Registry Value Set) and found a modification to LegalNoticeText.

[Screenshot 08: LegalNoticeText registry set event](./screenshots/08-legal-notice-event.png)

To see the content of the note, I opened the Registry Editor and navigated to the key to read the value.

[Screenshot 09: Registry Editor showing ransom note](./screenshots/09-registry-editor.png)

### Task 4: Execute test T0003-3. What is the updated file extension?

I cleared the logs and executed the test.

Invoke-AtomicTest T0003-3

I switched to the Security logs and searched for Event ID 4663 (An attempt was made to access an object). By analyzing the Object Name and the operation, I identified the new file extension.

[Screenshot 10: File extension change in Security logs](./screenshots/10-file-extension.png)

### Task 5: Execute test T0003-4. What is the assigned value of the malicious registry value?

For the final task, I cleared the logs and ran the test.

Invoke-AtomicTest T0003-4

I checked the Sysmon/Operational logs for Event ID 1 (Process Creation). In the CommandLine field, I found the command used to set the registry value, which revealed the answer.

[Screenshot 11: Malicious command line in Sysmon](./screenshots/11-command-line.png)

---

# Summary

This room further developed my Purple Teaming skills. I practiced:

- Script Modification: Altering existing Atomic Red Team scripts to expand the scope of a test (finding .bak files).
- User Account Monitoring: Detecting account creation via Security Event ID 4720.
- Registry Analysis: Using Sysmon Event ID 13 to detect persistence mechanisms and system defacement (LegalNoticeText).
- File Integrity Monitoring: Detecting file renaming and extension changes via Security Event ID 4663.