# TryHackMe Room: Intro To Threat Emulation

This room introduced the concept of Threat Emulation, distinguishing it from simpler threat simulation and outlining the structured methodologies and processes involved. The core focus was on understanding how real-world adversary intelligence (TTPs) is translated into actionable testing scenarios to bolster an organization's security defenses.

---

## Section: What is Threat Emulation?

### Task 1: What can be defined as an intelligence-driven impersonation of real-world attacks?

The process of taking specific threat intelligence and exactly replicating real-world attack scenarios and TTPs in a controlled environment is known as Threat emulation.

### Task 2: What is the exercise of representing adversary functions through predefined and automated attack patterns?

When the exercise combines TTPs from one or more groups without being an exact imitation of a specific adversary, it is called Threat simulation.

---

## Section: Emulation Methodologies

### Task 1: Under TIBER-EU, under which phase would Engagement and Scoping fall?

The TIBER-EU methodology is divided into several phases. The formal launch, planning, and scope approval (Engagement and Scoping) occur during the preparation phase.

### Task 2: What is the library that provides technical emulation tests based on TTPs?

The open-source library developed by Red Canary that provides modular, executable tests mapped to MITRE ATT&CK for testing defenses is the Atomic Red Team.

---

## Section: Threat Emulation Process I

### Task 1: There's a set of 3 software used by FIN6 & FIN7. Can you identify them? Answers are in alphabetical order, separated by a comma.

By examining the MITRE ATT&CK profiles for the threat groups FIN6 and FIN7, I found three shared tools in their Software and Techniques lists.

The three shared software tools are AdFind, Cobalt Strike, mimikatz.

[Screenshot 01: FIN6 common software listing](./screenshots/01-fin6-software.png)

[Screenshot 02: FIN7 common software listing](./screenshots/02-fin7-software.png)

### Task 2: Which factor will be considered when analysing whether to use existing or custom tools during the emulation?

When planning an emulation, the complexity of the adversary's TTPs must be assessed. This factor, TTP Complexity, determines whether existing open-source tools or custom-developed tools are required.

---

## Section: Threat Emulation Process II

### Task 1: The emulation plan component determining which activities are to be conducted is known as the?

The Scope is the crucial component of the emulation plan that explicitly defines the boundaries—the departments, users, devices, and activities upon which emulation activities are permitted.

### Task 2: What is flag one obtained after completing the exercise?

I completed the interactive exercise by correctly answering the questions related to the FIN7 threat group's capabilities (Darkside ransomware) and their emulation plan (Carbon Spider).

- Special ability: darkside
- Q1 (Execution): windows command shell
- Q2 (Persistence): Scheduled Task

[Screenshot 03: Flag one after completing exercise](./screenshots/03-flag-one.png)

### Task 3: What is flag two obtained after completing the exercise?

I completed the second round of the interactive exercise by correctly answering the questions related to the Reaper threat group's capabilities and their emulation plan.

- Special Ability (Zero Day): Adobe Flash
- Q1 (Initial Access): Drive-by Compromise
- Q2 (C2 Platform): dropbox

[Screenshot 04: Flag two after completing exercise](./screenshots/04-flag-two.png)

---

## Section: Threat Emulation Process III

### Task 1: Click the View Site button at the top of the task to launch the static site. What is flag three obtained after completing the exercise?

I completed the third round of the exercise, focusing on mitigation strategies against APTs and historical malware.

- Special Ability (Worm-like ransomware): WannaCry
- Q1 (Mitigating Command & Scripting): Execution Prevention
- Q2 (Mitigating Ransomware): Data Backup

[Screenshot 05: Flag three after completing exercise](./screenshots/05-flag-three.png)

### Task 2: What is flag four obtained after completing the exercise?

I completed the final round of the exercise, focusing on the Hafnium group and identifying indicators in web logs.

- Special Ability (Outlook web app exploitation): Hafnium
- Q1 (Successful SQLi Indicator): Presence of SQL syntax in logs
- Q2 (Brute-Force Indicator): Repeated login attempts from the same ip

[Screenshot 06: Flag four after completing exercise](./screenshots/06-flag-four.png)

--- 

# Summary

This room successfully introduced the foundational concepts of Threat Emulation and its practical application. I learned:

- Terminology: Differentiating between the precise replication of Threat Emulation and the generalized Threat Simulation.
- Methodologies: Understanding the phases of structured frameworks like TIBER-EU.
- Process Flow: How to translate intelligence (like FIN7's TTPs) into an Emulation Plan by defining the scope, tools, and objectives.
- Mitigation Mapping: The importance of mapping emulation results to practical mitigation strategies like Execution Prevention and Data Backup.