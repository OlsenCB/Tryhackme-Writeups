# TryHackMe Room: Sigma

This room focused on the Sigma generic signature format for SIEM systems. I learned the syntax of Sigma rules, how to convert them into vendor-specific queries (like Elastic Stack/Kibana), and how to apply them in a practical scenario to detect malicious activity such as AnyDesk installation, scheduled task creation, and ransomware behavior.

---

## Section: Sigma Rule Syntax

### Task 1: Which status level could lead to false results or be noisy, but could also identify interesting events?

The documentation defines the Experimental status as being very generic. While it allows for identifying interesting events, it is prone to being noisy and generating false positives.

### Task 2: The rule detection comprises two main elements: __ and condition expressions.

A Sigma rule's detection section is made up of Search Identifiers and condition expressions.

### Task 3: What two data structures are used for the search identifiers?

The definition of search identifiers typically relies on two specific data structures: lists and maps.

---

## Section: Rule Writing & Conversion

### Task 1: What command line tool is used to convert Sigma rules?

The Python-based tool used to convert generic Sigma rules into backend-specific SIEM queries is Sigmac.

### Task 2: At what time was the AnyDesk installation event created? [MMM DD, YYYY @ HH:MM:SS]

To find this, I first combined the snippets provided in the instructions to form a complete Sigma Rule:

title: AnyDesk Installation
status: experimental
description: AnyDesk Remote Desktop installation can be used by attacker to gain remote access.
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        CommandLine|contains|all: 
            - '--install'
            - '--start-with-win'
        CurrentDirectory|contains:
            - 'C:\ProgramData\AnyDesk.exe'
    condition: selection
falsepositives:
    - Legitimate deployment of AnyDesk
level: high
references:
    - https://twitter.com/TheDFIRReport/status/1423361119926816776?s=20
tags:
    - attack.command_and_control
    - attack.t1219

I then used Uncoder.io to convert this rule into an Elastic Stack Query. The resulting query was:

(process.command_line.text: - install AND process.command_line.text: - start-with-win AND process.working_directory.text:C\:\\ProgramData\\AnyDesk.exe)

I ran this query in Kibana (Elastic), ensuring the time range included dates before 2022. There was exactly one hit.

[Screenshot 01: AnyDesk installation event](./screenshots/01-anydesk-event.png)

The timestamp of this event was the answer.

### Task 3: What version of AnyDesk was installed?

By examining the command line arguments in the event found in the previous task (visible in Screenshot 01), I could see the version number 7.0.10.

---

## Section: Practical Scenario

### Task 1: To detect the creation of the scheduled task, what detection value would be appropriate for the Sigma rule?

I needed to create a rule to detect a scheduled task creation. Based on the documentation, I drafted the following Sigma rule:

title: Scheduled Task
status: test
description: detect Scheduled task
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        Image: '\schtasks.exe'
        CommandLine|contains|all: 
            - 'schtasks'
            - 'create'
    filter:
        User: 'NT AUTHORITY\SYSTEM'
    condition: selection and not filter

The resulting Elastic query was:

(process.executable.text: "\schtasks.exe" AND (process.command_line.text: "schtasks" AND process.command_line.text:"create")) AND  NOT (User.text:"NT AUTHORITY\SYSTEM")

The detection value used for the Image field is \schtasks.exe.

### Task 2: What was the name of the scheduled task created?

I executed the generated Elastic query in Kibana. It returned a single event. Examining the command line details, the name of the task was spawn.

[Screenshot 02: Scheduled task command line](./screenshots/02-schtasks-command.png)

### Task 3: What time is this task meant to run?

Looking at the arguments in the same event (Screenshot 02), the task was scheduled to run at 20:10.

### Task 4: To detect ransomware activity, what logsource category would be appropriate for the Sigma rule?

Next, I needed to detect ransomware file creation. I drafted a second Sigma rule:

title: ransomeware
status: test
description: detect ransomeware
logsource:
    category: file_event
    product: windows
detection:
    selection:
        Image: '\cmd.exe'
        TargetFilename|endswith: '.txt' 
    condition: selection

The Elastic query derived from this was:

(process.executable.text:"cmd.exe" AND file.path.text:*.txt)

For detecting file creation events, the appropriate logsource category is file_event.

### Task 5: What is the name of the created file?

I ran the query for the ransomware activity. The file name found in the results was YOUR_FILES.txt.

[Screenshot 03: Ransomware file name](./screenshots/03-ransomware-file.png)

### Task 6: What was the event code associated with the activity?

The event code associated with file creation (Sysmon) is 11.

[Screenshot 04: File creation event code](./screenshots/04-event-code-11.png)

### Task 7: What were the contents of the created ransomware file?

To find the content, I searched specifically for the file YOUR_FILES.txt. Viewing the latest event and inspecting the process.command_line field revealed the text echoed into the file.

[Screenshot 05: Ransomware file contents](./screenshots/05-file-contents.png)

---

# Summary

This room was a great exercise in converting abstract detection logic into concrete queries. I learned:

- The structure of Sigma Rules (Logsource, Detection, Condition).
- How to use Uncoder.io to translate Sigma into KQL (Kibana Query Language).
- How to hunt for specific threats like AnyDesk abuse, Scheduled Task persistence, and Ransomware file creation using these queries.