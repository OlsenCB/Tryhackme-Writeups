# TryHackMe Room: Custom Alert Rules in Wazuh

This room provided a practical, hands-on introduction to customizing and fine-tuning alert rules in Wazuh. The focus was on understanding the flow of data from logs through Decoders and into the Ruleset, learning how to write custom rules in local_rules.xml, and managing rule order and classification levels for effective security monitoring.

---

## Section: Decoders

### Task 1: Looking at the Sysmon Log, what will the value of sysmon.commandLine be?

By examining the provided Sysmon log structure and mentally stripping away the necessary JSON escaping characters, I extracted the clean command line string.

The value is: "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" "-file" "C:\Users\Alberto\Desktop\test.ps1"

### Task 2: What would the extracted value be if the regex is set to "User: \S*"?

Applying the regex pattern "User: \S*" (which matches "User: " followed by one or more non-whitespace characters) to the provided Sysmon log snippet: "srcuser": "WIN-P57C9KN929H\\Alberto", the extracted value would be: WIN-P57C9KN929H\\Alberto.

---

## Section: Rules

### Task 1: From the Ruleset Test results, what is the <mitre> ID of rule id 184666?

I inspected the "Ruleset Test Output" section provided in the task instructions, looking specifically for the <mitre> tag associated with the triggered rule ID 184666.

The MITRE ID is T1055.

[Screenshot 01: MITRE ID for rule 184666](./screenshots/01-mitre-id.png)

### Task 2: According to the Wazuh documentation, what is the description of the rule with a classification level of 12?

I consulted the official Wazuh documentation on "Rules classification" to find the predefined description for classification level 12, which represents the highest severity alerts.

The description is: high priority attack.

[Screenshot 02: Description of rule classification level 12](./screenshots/02-rule-classification-12.png)

### Task 3: In the Ruleset Test page, change the value of "sysmon.image" to 'taskhost.exe', and press the "Test" button again. What is the ID of the rule that will get triggered?

I used the Wazuh UI's "Ruleset Test" tool, pasted the Sysmon log, and manually modified the sysmon.image value from svchost.exe to taskhost.exe. Running the test triggered a new rule based on this specific image name.

The triggered rule ID is 184717.

[Screenshot 03: Rule ID triggered by taskhost.exe](./screenshots/03-taskhost-rule-id.png)

---

## Section: Rule Order

### Task 1: In the sysmon_rules.xml file, what is the Rule ID of the parent of 184717?

I navigated to the official Wazuh GitHub repository and located the main 0330-sysmon_rules.xml file. I searched for rule ID 184717. The parent rule ID is specified by the if_sid tag within the rule definition.

The parent Rule ID is 184601.

[Screenshot 04: Parent rule ID for 184717](./screenshots/04-parent-rule-id.png)

---

## Section: Custom Rules

### Task 1: What is the regex field name used in the local_rules.xml?

By reviewing the custom rule configuration logic provided in the task's instructions, I identified the specific field name being targeted by the regular expression for filtering.

The regex field name is audit.cwd.

### Task 2: Looking at the log, what is the current working directory (cwd) from where the command was executed?

I examined the raw log snippet provided in the section description. The current working directory (cwd) is explicitly listed within the description of the audit log entry.

The current working directory is /var/log/audit.

[Screenshot 05: Current working directory (cwd) in log](./screenshots/05-cwd-directory.png)

---

## Section: Fine-Tuning

### Task 1: If the filename in the logs is "test.php", what rule ID will be triggered?

I examined the ruleset provided for fine-tuning. The rule that specifically matches any filename with the .php extension is triggered.

The triggered rule ID is 100003.

[Screenshot 06: Rule ID triggered by .php extension](./screenshots/06-php-rule-id.png)

---

## Section: Fine-Tuning

### Task 1: If the filename in the logs is "test.php", what rule ID will be triggered?

I examined the ruleset provided for fine-tuning. The rule that specifically matches any filename with the .php extension is triggered.

The triggered rule ID is 100003.

[Screenshot 06: Rule ID triggered by .php extension](./screenshots/06-php-rule-id.png)

## Task 2: If the filename in the logs is "malware-checker.sh", what is the rule classification level in the generated alert?

This is a key example of how rule order and level hierarchy work. Although a specific rule (ID 100004) exists for the exact filename malware-checker.sh with level 0 (silencing it), the rule below it (ID 100005) is a broader rule that matches any filename containing the word "malware" and sets a high classification level. Since rules are processed top-down and the highest level is retained, the classification level will be determined by the most severe matching rule.

The generated alert classification level is 12.

[Screenshot 07: Highest classification level for "malware" filename](./screenshots/07-malware-level-12.png)

---

# Summary

This room provided a fundamental understanding of Wazuh's rule processing engine, essential for any SOC analyst working with the platform. Key skills developed include:

-Decoder Analysis: Manually extracting field values from complex log formats (like Sysmon) to understand how decoders work.
-Ruleset Testing: Utilizing the Ruleset Test tool to identify which rules are triggered by specific log events.
-Custom Rule Creation: Learning how to write new, targeted rules in local_rules.xml by defining criteria based on specific fields (e.g., audit.cwd).
-Rule Hierarchy Management: Understanding how if_sid links parent and child rules, and critically, how the highest-level severity rule takes precedence in determining the final alert classification level.