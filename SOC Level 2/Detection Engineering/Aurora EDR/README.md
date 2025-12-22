# TryHackMe Room: Aurora EDR

This room introduced me to Aurora, a lightweight and customizable Endpoint Detection and Response (EDR) agent for Windows. I learned about the fundamentals of EDR, how Windows Event Tracing (ETW) works, and how to analyze Aurora's event logs to detect malicious activity like reconnaissance and ransomware.

---

## Section: EDR Definition

### Task 1: What does EDR stand for?

As defined in the room, an EDR solution provides proactive threat detection and visibility through real-time monitoring of endpoints. The acronym stands for Endpoint Detection and Response.

---

## Section: Event Tracing for Windows

### Task 1: Which applications produce event logs?

In the context of ETW, the applications that generate and produce event logs are called Providers.

### Task 2: Applications that subscribe to event logs are called?

Conversely, the applications that subscribe to listen to these events in real-time or from a file are called Consumers.

### Task 3: Which event level would be used to describe a significant problem with a service?

The event level used to describe a significant issue with a service or application is Error.

### Task 4: What is the Windows Eventlog category responsible for recording events associated with programs currently running called?

The category that records events related to programs installed and running on the system is System.

---

## Section: Aurora

### Task 1: Which Aurora preset supports the highest CPU limit?

I reviewed the comparison table provided in the room documentation regarding Aurora presets. The preset that allows for the highest CPU usage is Intense.

[Screenshot 01: Aurora presets table](./screenshots/01-aurora-presets.png)

### Task 2: Which process would be terminated when the response flag ancestors:3 is used?

The ancestors flag targets a process's lineage. The logic is: 1 for the parent, 2 for the grandparent. Therefore, ancestors:3 targets the Great grandparent.

### Task 3: When the Aurora agent is terminated, which event id will be used?

According to the documentation, the Event ID generated when the Aurora Agent is terminating is 103.

---

## Section: Interactive Scenario

### Task 1: What is the title of the first event rule?

To generate the necessary logs, I performed the following steps:

1. Right-clicked Aurora_launch.bat and selected Run as administrator.
2. Waited for the timer to finish before pressing any key.
3. Opened Event Viewer and clicked Create Custom View.
4. I selected all "Event levels".
5. In "Event sources", I selected aurora_agent and auroraAgent.

After filtering, I looked at the very first event and inspected the EventData section.

[Screenshot 02: EventData for the first event](./screenshots/02-first-event-data.png)

The Rule Title was Process Reconnaissance Via Wmic.EXE.

### Task 2: What is the Rule ID of the matched Sigma rule?

Using the same event details from the previous task, I found the Rule ID in the event data. Answer: 221b251a-357a-49a9–920a-271802777cc0

### Task 3: What is the Sigma rule level of the event?

The level was also listed in the event details: medium.

### Task 4: What is the Rule Title of the second Event?

I clicked on the second event in the list to find the answer.

[Screenshot 03: Second event details](./screenshots/03-second-event.png)

The answer is Whoami Execution.

### Task 5: Based on the Rule's characteristics, what malicious activity is associated with the event?

Analyzing the characteristics of the rule triggered in the second event, the associated malicious activity is ransomware.

---

# Summary

In this room, I explored the Aurora EDR agent. I learned:

-The terminology of Event Tracing for Windows (ETW) (Providers vs. Consumers).
-How to configure Aurora presets and understand response flags like ancestors.
-How to generate malicious traffic in a sandbox and analyze the resulting Aurora logs using the Windows Event Viewer to identify specific threats like reconnaissance and ransomware attempts.