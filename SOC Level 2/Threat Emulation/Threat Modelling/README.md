# TryHackMe Room: Threat Modelling

This room provided a comprehensive deep dive into the theoretical and practical aspects of Threat Modelling. I learned how to identify potential security risks before they manifest by using industry-standard frameworks like STRIDE, DREAD, and PASTA, as well as leveraging the MITRE ATT&CK knowledge base.

---

## Section: Threat Modelling Overview

### Task 1: What is a weakness or flaw in a system, application, or process that can be exploited by a threat?

The definition provided describes a fundamental security concept: a weakness arising from bugs, misconfigurations, or design flaws that can lead to harm.

Answer: Vulnerability

### Task 2: Based on the provided high-level methodology, what is the process of developing diagrams to visualise the organisation's architecture and dependencies?

To effectively model threats, one must first know what needs protecting. This step involves creating architectural diagrams and classifying data importance (e.g., customer data, IP).

Answer: Asset Identification

### Task 3: What diagram describes and analyses potential threats against a system or application?

This is a graphical representation used to decompose attack scenarios into smaller components, where the root node represents the attacker's main goal.

Answer: Attack tree

---

## Section: Modelling with MITRE ATT&CK

### Task 1: What is the technique ID of "Exploit Public-Facing Application"?

I searched the MITRE ATT&CK Enterprise matrix for the specific technique regarding public-facing applications.

[Screenshot 01: MITRE ATT&CK T1190 details](./screenshots/01-mitre-t1190.png)

Answer: T1190

### Task 2: Under what tactic does this technique belong?

Looking at the column header for T1190 in the matrix, it falls under the very first stage of an attack.

Answer: Initial Access

---

## Section: Mapping with ATT&CK Navigator

### Task 1: How many MITRE ATT&CK techniques are attributed to APT33?

To answer this, I launched the provided machine and opened the ATT&CK Navigator.

1. I clicked Create New Layer -> Enterprise.
2. Using the search function (magnifying glass), I searched for APT 33.
3. In the "Techniques" section next to the selection, I checked the "select" count.

[Screenshot 02: Count of APT33 techniques in Navigator](./screenshots/02-apt33-count.png)

### Task 2: Upon applying the IaaS platform filter, how many techniques are under the Discovery tactic?

I went to the Filters settings in Navigator, deselected all platforms, and selected only IaaS. I then looked at the Discovery column to count the remaining highlighted techniques.

[Screenshot 03: Discovery techniques under IaaS filter](./screenshots/03-iaas-discovery.png)

---

## Section: DREAD Framework

### Task 1: What DREAD component assesses the potential harm from successfully exploiting a vulnerability?

DREAD is a risk scoring model. The "D" stands for the component that evaluates the severity of the outcome (data loss, downtime, etc.).

Answer: Damage

### Task 2: What DREAD component evaluates how others can easily find and identify the vulnerability?

This component measures how hard it is for an attacker to locate the flaw (e.g., is it on a public interface or hidden deep in the backend?).

Answer: Discoverability

### Task 3: Which DREAD component considers the number of impacted users when a vulnerability is exploited?

This component calculates the scope of the impact in terms of the user base.

Answer: Affected Users

---

## Section: STRIDE Framework

### Task 1: What foundational information security concept does the STRIDE framework build upon?

STRIDE is designed to help identify threats that violate specific security properties. These properties map directly to the core pillars of information security.

Answer: CIA Triad

### Task 2: What policy does Information Disclosure violate?

Information Disclosure is the "I" in STRIDE. It directly opposes the principle of keeping data secret.

Answer: Confidentiality

### Task 3: Which STRIDE component involves unauthorised modification or manipulation of data?

The "T" in STRIDE refers to changing data or code without permission, violating Integrity.

Answer: Tampering

### Task 4: Which STRIDE component refers to the disruption of the system's availability?

The "D" (last letter) in STRIDE refers to attacks that prevent legitimate users from accessing the system.

Answer: Denial of Service

### Task 5: Provide the flag for the simulated threat modelling exercise.

I completed the interactive simulation by matching threats to their STRIDE categories:

1. Spoofing violates Authentication.
2. Tampering violates Integrity.
3. Repudiation violates Non-repudiation.
4. Information Disclosure violates Confidentiality.
5. Denial of Service violates Availability.
6. Elevation of Privilege violates Authorization.

[Screenshot 04: Interactive STRIDE question 1](./screenshots/04-stride-q1.png)

[Screenshot 05: Interactive STRIDE question 2](./screenshots/05-stride-q2.png)

[Screenshot 06: Interactive STRIDE question 3](./screenshots/06-stride-q3.png)

[Screenshot 07: Interactive STRIDE question 4](./screenshots/07-stride-q4.png)

After correctly identifying the threats, I received the flag.

[Screenshot 08: STRIDE simulation flag](./screenshots/08-stride-flag.png)

---

## Section: PASTA Framework

### Task 1: In which step of the framework do you break down the system into its components?

PASTA is a risk-centric framework. The step involving mapping data flows, trust boundaries, and attack surfaces is:

Answer: Decompose the Application

### Task 2: During which step of the PASTA framework do you simulate potential attack scenarios?

This step involves evaluating the likelihood and impact by actively simulating how an attacker might strike.

Answer: Analyse the Attacks

### Task 3: In which step of the PASTA framework do you create an inventory of assets?

Before analyzing threats, one must understand the technology stack (hardware, software, dependencies).

Answer: Define the Technical Scope

### Task 4: Provide the flag for the simulated threat modelling exercise.

This was a comprehensive simulation involving a meeting with various stakeholders (Business Analyst, System Architect, Lead Developer, Security Engineer) regarding an Online Banking Platform.

I navigated the conversation through these stages:

1. Strategic Planning
2. System Architecture
3. Software Development
4. Information Security
5. Strategic Planning (Review)

Questions & Answers during the simulation:

Q1 (Business Analyst): What is the top priority?

- Answer: Protecting customers' personal and financial data, securing transactions, and ensuring service availability.

Q2 (System Architect): What are the primary technical assets?

- Answer: Amazon EC2, RDS, and S3 services

Q3 (Lead Developer): What components were highlighted during decomposition?

- Answer: User registration, account management, fund transfers, bill payments, and account statements

Q4 (Security Engineer): Which threat is NOT considered?

- Answer: Social engineering attacks

Q5 (Security Engineer): Which vulnerability is a potential issue?

- Answer: Cloud Infrastructure Misconfigurations

Q6 (Security Engineer): Which mitigation strategy matches the threats?

- Answer: Account lockouts

Q7 (Business Analyst): What is the potential consequence?

- Answer: Financial loss and significant reputational damage

Upon completing the meeting, I obtained the final flag.

[Screenshot 09: PASTA simulation flag](./screenshots/09-pasta-flag.png)

---

# Summary

This room was excellent for understanding that security starts at the design phase, not just the testing phase. I learned:

- STRIDE is developer-focused, mapping threats to the CIA triad.
- DREAD provides a quantitative way to score and prioritize risks.
- PASTA aligns technical security efforts with business objectives.
- MITRE ATT&CK is an invaluable resource for mapping real-world adversary behaviors to theoretical models.