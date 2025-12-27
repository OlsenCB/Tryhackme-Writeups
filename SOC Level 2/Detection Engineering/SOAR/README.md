# TryHackMe Room: SOAR

This room provided a conceptual and practical overview of SOAR (Security Orchestration, Automation, and Response). I explored the evolution of Security Operations Centers (SOCs), the problems they face (like alert fatigue), and how SOAR platforms help by integrating tools and automating repetitive tasks through playbooks.

---

## Section: Security Operations Centres

### Task 1: Under which SOC generation did SIEM tools emerge?

According to the room's reading material, the First Generation relied on antivirus, while the Second-Generation saw the emergence of SIEM tools to add to previous SOC functions.

Answer: Second

### Task 2: How would you describe the experience of having an overload of security events being triggered within a SOC?

The use of numerous security tools often results in a huge number of triggered alerts. When analysts become overwhelmed by these (often false positive) alerts and cannot address serious events, this is known as Alert fatigue.

---

## Section: Security Orchestration, Automation and Response

### Task 1: The act of connecting and integrating security tools and systems into seamless workflows is known as?

Just like organizing a musical orchestra, the act of connecting and integrating various security tools into streamlined processes/workflows is defined as Security Orchestration.

### Task 2: What do we call a predefined list of actions to handle an incident?

A structured checklist of actions used to detect, respond to, and handle threat incidents is called a Playbook.

---

## Section: SOAR Workflows

### Task 1: Are manual analyses vital within a SOAR workflow? yay or nay?

Even with automation, human logic is often required. For example, if an automated check (like VirusTotal) returns no results, a manual analysis is necessary to determine if a file is malicious.

Answer: yay

---

## Section: Threat Intel Workflow Practical

### Task 1: What is the flag received?

This task involved an interactive exercise where I had to configure the settings for a SOAR workflow to automate security investigations.

Q1: Adjust the Settings for automated and manual flows to adopt a SOAR.

1. Case Management Settings: I configured the system to automatically create cases for high-severity alerts.

[Screenshot 01: Case Management Settings configuration](./screenshots/01-case-management.png)

2. Threat Intelligence Feeds: I selected the appropriate threat intel sources to enrich the data.

[Screenshot 02: Threat Intelligence Feeds configuration](./screenshots/02-threat-intel-feeds.png)

3. Incident Data Extraction: I set up the rules for extracting relevant artifacts (IPs, hashes, etc.) from the incidents.

[Screenshot 03: Incident Data Extraction configuration](./screenshots/03-data-extraction.png)

4. Reputation Checks: I configured automated reputation lookups against known bad databases.

[Screenshot 04: Reputation Checks configuration](./screenshots/04-reputation-checks.png)

5. Course of Action: Finally, I defined the response actions based on the analysis results.

[Screenshot 05: Course of Action configuration](./screenshots/05-course-of-action.png)

After completing the configuration, I received the flag.

---

# Summary

In this room, I learned:

- The history of SOC evolution and the issue of Alert Fatigue.
- The core components of SOAR: Orchestration (connecting tools) and Automation (executing actions without human intervention).
- The importance of Playbooks in standardizing incident response.
- How to configure a basic SOAR workflow, moving from case creation and data extraction to reputation checks and final response actions.