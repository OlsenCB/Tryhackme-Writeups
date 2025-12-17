# TryHackMe Room: Tactical Detection

This room focused on the practical application of threat intelligence and detection engineering. I explored how to transform Indication of Compromise (IOCs) into actionable detection rules using Sigma, translated those rules for different SIEMs using Uncoder.io, and set up "tripwires" using Windows Event Logs to detect access to sensitive files.

---

## Section: Unique Threat Intel

### Task 1: What did we use to transform IOCs as detection rules in a vendor-agnostic format?

The industry standard for writing vendor-agnostic detection rules, as introduced in this room, is Sigma.

### Task 2: What is the original indicator found by the authors of the documentation? Write it as written in the spreadsheet.

I reviewed the threat intelligence spreadsheet provided in the task. The original domain indicator listed was bad3xe69connection.io.

### Task 3: What is the full file path of the malicious file downloaded from the internet?

According to the documentation, the full path where the malware landed was C:\Downloads\bad3xe69.exe.

### Task 4: In the Sigma Rule baddomains.yml, what is the logsource category used by the author?

I opened the baddomains.yml Sigma rule file. Under the logsource section, the category was defined as proxy.

---

## Section: Publicly Generated IOCs

### Task 1: Upon translating the Follina Sigma Rule, what is the index name that the rule will be using, as shown in the output?

For this task, I navigated to uncoder.io and selected the Uncoder AI interface.

1. On the left (Input), I selected Sigma.

2. On the right (Output), I selected ElastAlert.

3. I copied the Follina_MSDT Sigma Rule provided in the room and pasted it into the input box.

4. I clicked Translate.

[Screenshot 01: Uncoder translation for Follina rule](./screenshots/01-uncoder-follina.png)

Checking the output configuration, the index name was winlogbeat-*.

### Task 2: What is the Alerter subclass, as shown in the output?

In the same ElastAlert output, the alert field (subclass) was set to debug.

### Task 3: Change the Uncoder output to Elastic Stack Query (Lucene). Which part of the ElastAlert output looks exactly like the Elastic Query?

I changed the output format on Uncoder from ElastAlert to Elastic Stack Query (Lucene). Comparing the two outputs, the filter section of the ElastAlert rule matched the Lucene query.

[Screenshot 02: Comparison of ElastAlert and Lucene queries](./screenshots/02-elastalert-vs-lucene.png)

### Task 4: Translate the Log4j Sigma Rule into a Splunk Alert (SPL). What is the alert severity, as shown in the output?

I switched the input content to the Log4j Sigma Rule provided in the task and changed the output format to Splunk Alert.

[Screenshot 03: Log4j rule translation to Splunk](./screenshots/03-log4j-splunk.png)

The output showed the alert.severity was set to 3.

### Task 5: What is the dispatch.earliest_time value, as shown in the output?

Looking at the Splunk Alert configuration properties in the output, the dispatch.earliest_time was -60m@m.

### Task 6: Change the Uncoder output to Splunk Query (SPL). What is the source, as shown in the output?

Finally, I changed the output format to standard Splunk Query (SPL). The query specified the source as WinEventLog:Security.

---

## Section: Leveraging "Know Your Environment": Tripwires

### Task 1: What is the "Accesses" value in the log details when you try reading our Secret Document's contents via cmd?

I followed the instructions to access the secret file via the command line. This action triggered specific Windows Audit logs. I opened Event Viewer and looked for Event ID 4663 (An attempt was made to access an object). The "Accesses" field contained the specific permissions requested.

[Screenshot 04: Event ID 4663 details](./screenshots/04-event-4663.png)

### Task 2: Event ID 4663 is always preceded by?

In the Event Viewer, immediately before the 4663 event, I observed an Event ID 4656 (A handle to an object was requested).

### Task 3: What Event ID signifies the closure of an "object"?

After the operation was complete, the system logged Event ID 4658, which signifies that the handle to the object was closed.

[Screenshot 05: Event ID 4658 object closure](./screenshots/05-event-4658.png)

### Task 4: Event ID 4658 helps determine how long a specific object was open. What description field will you check in between events to be able to do so?

To correlate the request (4656), access (4663), and close (4658) events for the same operation, the Handle ID field is used as the unique identifier.

[Screenshot 06: Handle ID field description](./screenshots/06-handle-id.png)

---

## Section: Purple Teaming

### Task 1: Fill in the Blanks: The Tempest and Follina rooms are examples of leveraging ______ ____ tactics.

Combining offensive knowledge (Red) with defensive detection (Blue) is known as Purple team tactics.

### Task 2: What CVE is the Follina MSDT room about?

The Follina vulnerability involving the Microsoft Support Diagnostic Tool (MSDT) is identified as CVE-2022-30190.

---

# Summary

In this room, I practiced the practical side of Detection Engineering by:

-Transforming raw Indicators of Compromise (IOCs) into vendor-agnostic Sigma rules.
-Using Uncoder.io to translate detection rules between different formats (Sigma to ElastAlert, Splunk SPL, and Lucene).
-Setting up "tripwires" using Windows Object Access Auditing to detect unauthorized access to sensitive files.
-Analyzing specific Windows Event IDs (4656, 4663, and 4658) to track file handles and determine access duration.
-Understanding Purple Teaming concepts to bridge the gap between offensive knowledge and defensive implementation.

This room bridged the gap between raw threat intelligence and practical SIEM implementation.