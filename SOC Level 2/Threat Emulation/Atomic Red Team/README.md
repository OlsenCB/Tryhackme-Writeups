# TryHackMe Room: Atomic Red Team

This room focused on Atomic Red Team, a library of simple, modular tests mapped to the MITRE ATT&CK framework. I learned how to use the Invoke-AtomicRedTeam PowerShell framework to execute these tests (Atomics), understand their structure (YAML), and validate detection capabilities by observing the generated artifacts in logs (Sysmon).

---

## Section: Atomic Red Team

### Task 1: What type of executor is used for actions that cannot be automated?

According to the Atomic Red Team documentation structure, when a test involves steps that require human intervention (like GUI interactions) and cannot be purely automated, the executor type is defined as Manual.

### Task 2: What is the field in an Atomic YAML file populated by a unique identifier to isolate a specific Atomic?

Every test within an Atomic YAML file is assigned a unique ID to distinguish it from others. This field is called auto_generated_guid.

### Task 3: What is the field in an Atomic YAML file populated by commands for deleting files used for emulation or reverting modified configurations?

To ensure the system is returned to its original state after a test, specific commands are defined to delete artifacts or revert changes. This field is cleanup_command.

---

## Section: Invoke-AtomicRedTeam

### Task 1: How many atomic tests are under Atomic T1110.001 that are supported on Windows hosts?Task 1: How many atomic tests are under Atomic T1110.001 that are supported on Windows hosts?

I used the Invoke-AtomicTest cmdlet with the -ShowDetailsBrief parameter to list the available tests for this specific technique ID.

Invoke-AtomicTest T1110.001 -ShowDetailsBrief

The output showed 4 supported tests.

[Screenshot 01: Tests supported for T1110.001](./screenshots/01-t1110-tests.png)

### Task 2: What is the Atomic name of the second test under Atomic T1218.005?

To find the name of a specific test, I queried the details for the technique.

Invoke-AtomicTest T1218.005 -ShowDetails

[Screenshot 02: Atomic name for T1218.005](./screenshots/02-t1218-name.png)

### Task 3: How many prerequisites are not met for Atomic T1003?

I checked the prerequisites for the T1003 technique using the -CheckPrereqs switch.

Invoke-AtomicTest T1003 -CheckPrereqs

[Screenshot 03: Unmet prerequisites for T1003](./screenshots/03-t1003-prereqs.png)

### Task 4: Executes tests based on the test GUID

Reviewing the help documentation or available parameters for the cmdlet reveals that to execute a specific test using its unique identifier, you use the TestGuids parameter.

### Task 5: What is the name of the scheduled task created after executing the 2nd test of Atomic T1053.005?

I executed the specific test number (2) for the technique T1053.005.

Invoke-AtomicTest T1053.005 -TestNumbers 2

[Screenshot 04: Scheduled task created by T1053.005](./screenshots/04-scheduled-task.png)

### Task 6: What is the registry key modified after executing the 2nd test of Atomic T1547.001?

I viewed the details for the second test of T1547.001 to identify the targeted registry key.

Invoke-AtomicTest T1547.001-2 -ShowDetails

[Screenshot 05: Registry key modified by T1547.001](./screenshots/05-registry-key.png)

---

## Section: Revisiting MITRE ATT&CK

### Task 1: Using the ATT&CK Navigator, how many techniques are attributed to admin@338?

Based on the instructions provided in the room (or by checking the ATT&CK Navigator layer for the group), there are 9 techniques attributed to admin@338.

### Task 2: Using the mapping provided by the ATT&CK Navigator, what is the Technique ID of the phishing technique used by the threat group?

Looking at the mapping for Initial Access, the group uses Spearphishing Attachment. Answer: T1566.001

### Task 3: How many Atomic tests on Atomic T1083 are supported on Windows hosts?

I checked the brief details for technique T1083.

Invoke-AtomicTest T1083 -ShowDetailsBrief

[Screenshot 06: Supported tests for T1083](./screenshots/06-t1083-supported.png)

### Task 4: What file should exist to satisfy the prerequisite of Atomic Test T1049-4?

I ran the prerequisite check for the specific test number.

Invoke-AtomicTest T1049–4 -CheckPrereqs

[Screenshot 07: File prerequisite for T1049-4](./screenshots/07-t1049-prereqs.png)

### Task 5: What is the echoed string upon executing Atomic Test T1059.003-3?

I executed the test to see the output.

Invoke-AtomicTest T1059.003–3

[Screenshot 08: Echoed string from T1059.003-3](./screenshots/08-echoed-string.png)

### Task 6: What is the hostname of the machine based on Atomic Test T1082-6?

I executed the system information discovery test.

Invoke-AtomicTest T1082–6

[Screenshot 09: Hostname from T1082-6](./screenshots/09-hostname.png)

### Task 7: How many accounts are disabled based on Atomic Test T1087.001-9?

I executed the account discovery test.

Invoke-AtomicTest T1087.001–9

The output indicated that 3 accounts were disabled.

---

## Section: Emulation to Detection

### Task 1: How many Sysmon events are generated after executing Atomic Test T1547.001-4?

I navigated to the Sysmon Operational logs (Applications and Services Logs/Microsoft/Windows/Sysmon/Operational) and cleared them. Then, I executed the test.

Invoke-AtomicTest T1547.001–4

After refreshing the Event Viewer, I counted the generated events.

[Screenshot 10: Sysmon events from T1547.001-4](./screenshots/10-sysmon-events.png)

### Task 2: Based on the same events from Q1, what is the file name created by the test?

I looked for Event ID 11 (File Create) in the generated logs. The file created was vbsstartup.vbs.

### Task 3: Based on the Registry Value Set event generated after executing Atomic Test T1547.001-13, what is the value of the TargetObject field?

I checked the details of the test to find the registry path targeted.

Invoke-AtomicTest T1547.001–13 -ShowDetails

[Screenshot 11: TargetObject registry path](./screenshots/11-target-object-path.png)

### Task 4: Excluding the WHOAMI detection, what is the title of the first rule triggered on Aurora EDR after executing Atomic Test T1547.001-7?

Note: The Aurora license on the machine had expired, so I could not verify this live. Based on external resources/writeups:

Answer: PowerShell Writing Startup Shortcuts

### Task 5: Excluding the WHOAMI detection, what is the title of the first rule triggered on Aurora EDR after executing Atomic Test T1547.001-8?

Similarly, based on external resources:

Answer: Persistence Mechanisms in Recycle Bin

---

## Section: Customizing Atomic Red Team

### Task 1: What parameter should you use to customise the input arguments interactively?

To modify the default arguments (like file paths or commands) during execution without editing the YAML file, you use the PromptForInputArgs parameter.

### Task 2: What parameter should you use in conjunction with InputArgs/PromptForInputArgs to revert the changes made by the test?

To clean up the artifacts created by a specific test configuration, you use the Cleanup parameter.

### Task 3: What is the default port used by the Atomic GUI?

The web-based GUI for Atomic Red Team listens on port 8487.

---

## Section: Case Study: Emulating APT37

### Task 1: Using the ATT&CK Navigator, how many techniques are attributed to APT37?

Checking the specific layer or documentation for APT37 reveals that there are 29 techniques attributed to this group.

[Screenshot 12: APT37 techniques in Navigator](./screenshots/12-apt37-navigator.png)

### Task 2: Using the mapping provided by the ATT&CK Navigator, what is the phishing technique used by the threat group?

Answer: Spearphishing attachment

[Screenshot 13: Phishing technique in Navigator](./screenshots/13-phishing-technique.png)

### Task 3: How many techniques attributed to APT37 have an existing Atomic file?

I identified the IDs for the 29 techniques and checked which ones existed in the atomics directory.

The IDs checked were: T1189, T1059, T1203, T1106, T1055, T1027, T1120, T1057, T1082, T1033, T1123, T1005, T1105, T1529.

I ran the following command to count the matches:

ls C:\Tools\AtomicRedTeam\atomics | Where-Object Name -Match "T1189|T1059|T1203|T1106|T1055|T1027|T1120|T1057|T1082|T1033|T1123|T1005|T1105|T1529"

[Screenshot 14: Count of APT37 atomic files](./screenshots/14-apt37-atomics-count.png)

### Task 4: Based on the results of Q3, which Atomic has no tests supported on Windows?

After reviewing the available tests for the matched techniques, I found that T1059.006 exists but has no supported tests for the Windows platform in this library.

### Task 5: What is the description of the prerequisite needed for Atomic Test T1055-1?

I checked the prerequisites for the process injection test.

Invoke-AtomicTest T1055.1 -CheckPrereqs

[Screenshot 15: Prerequisite description for T1055-1](./screenshots/15-t1055-prereqs.png)

### Task 6: How many Atomic tests have met the prerequisites for Atomic T1082?

I ran the check for the System Information Discovery technique.

Invoke-AtomicTest T1082 -CheckPrereqs

[Screenshot 16: Prerequisites check for T1082](./screenshots/16-t1082-prereqs.png)

### Task 7: What are the three event IDs logged based on the execution of Atomic Test 1547.001-3? Provide the IDs in ascending order (e.g. 1,2,3).

I cleared the Sysmon Operational logs, executed the test, and checked the Event Viewer.

Invoke-AtomicTest T1547.001–3

The three Event IDs generated were 1, 11, 13 (Process Create, File Create, Registry Value Set).

[Screenshot 17: Sysmon events for T1547.001-3](./screenshots/17-sysmon-ids.png)

### Task 8: What command is executed (with default input value) by Atomic Test T1529-1? Do not run without the ShowDetails parameter.

To view the potentially destructive command without running it, I used -ShowDetails.

Invoke-AtomicTest T1529–1 -ShowDetails

[Screenshot 18: Command executed by T1529-1](./screenshots/18-shutdown-command.png)

### Task 9: What is the value of the TargetFilename inside the File Creation log (Event ID 11) generated by Atomic Test T1106-1?

I cleared logs, ran the execution via API test, and checked Sysmon Event ID 11.

Invoke-AtomicTest T1106–1

[Screenshot 19: Full path of TargetFilename](./screenshots/19-target-filename.png)

### Task 10: How many events are generated by executing the cleanup actions of Atomic T1105?

I cleared the logs one last time and ran the cleanup command for the Ingress Tool Transfer technique.

Invoke-AtomicTest T1105 -Cleanup

[Screenshot 20: Event count for T1105 cleanup](./screenshots/20-cleanup-event-count.png)

---

# Summary

In this room, I gained hands-on experience with Atomic Red Team, a critical framework for validating security defenses. I learned how to:

- Execute Atomic Tests: Using the Invoke-AtomicTest PowerShell module to run specific techniques mapped to MITRE ATT&CK.
- Manage Dependencies: Checking and satisfying prerequisites (-CheckPrereqs) and cleaning up artifacts (-Cleanup) to maintain a clean environment.
- Emulate Threats: Simulating real-world adversary behaviors (like APT37) to test defensive capabilities.
- Validate Detections: Correlating executed tests with Sysmon logs (Event IDs 1, 11, 13) to verify if the activity was logged and potentially detectable by SIEM/EDR solutions.