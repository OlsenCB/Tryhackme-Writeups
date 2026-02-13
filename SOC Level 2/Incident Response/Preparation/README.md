# TryHackMe Room: Preparation

This room focuses on the very first phase of the Incident Response (IR) lifecycle: Preparation. Before an attack even happens, an organization must be ready. I explored the key components of a successful IR capability, covering the necessary people (CSIRT), documentation (policies and evidence tracking), and technology (tools and visibility).

---

## Section: Incident Response Capability

### Task 1: What is an observed occurrence within a system?

As defined in the room, any observed occurrence within a system or network—whether it's a user logging in, an email being sent, or an antivirus alert—is broadly classified as an Event.

### Task 2: What is described as a violation of security policies and practices?

When an event evolves into a violation of security policies or practices (e.g., data exfiltration, ransomware encryption, or denial of service), it becomes an Incident.

### Task 3: Under which incident response phase do organisations lay down their procedures?

The phase where organizations establish their CSIRT, define procedures, and ensure they can effectively react to a breach is Preparation.

### Task 4: Under which phase will an organisation resume business operations fully and update its response capabilities?

The final phase, where business operations return to normal, threats are removed, and the organization conducts a post-mortem to update training and capabilities, is Recovery and Lessons Learned.

---

## Section: People and Documentation Preparation

### Task 1: A group that handles events involving cyber security breaches, comprising individuals with different skills and expertise, is known as?

A specialized cross-functional group including technical staff, legal counsel, and PR experts authorized to make decisions during a cyber attack is the cyber security incident response team (CSIRT).

### Task 2: Which documents would be used to accompany any evidence collected and keeps track of who handles the investigation procedures?

To maintain the integrity of an investigation, especially for legal purposes, it is crucial to document who collected, handled, and stored evidence. These records are called chain of custody documents.

---

## Section: Technology Preparation

### Task 1: What would a kit containing the necessary incident-handling tools be called?

Responders need a pre-packed set of tools (forensic imaging software, analysis tools, storage media) ready to go at a moment's notice. This is often referred to as a jump bag.

---

## Section: Visibility

### Task 2: What is the Event ID for the File Created rule associated with the test?

I followed the instructions to simulate the T1486 technique (often associated with ransomware). Checking the logs for file creation events (typically Sysmon), I identified the specific Event ID.

The Event ID for File Created is 11.

[Screenshot 01: Event ID 11 File Created](./screenshots/01-event-id-11.png)

### Task 2: Under the Software Restriction Policies, what is the default security level assigned to all policies?

I opened the Local Security Policy tool on the VM and navigated to Software Restriction Policies -> Security Levels. The default setting indicated that software is allowed to run unless explicitly disallowed.

The level is Unrestricted.

[Screenshot 02: Software Restriction Policies Security Level](./screenshots/02-security-level.png)

### Task 3: Find the Audit Policy folder under Local Policies. What setting has been assigned to the policy Audit logon events?

Still within the Local Policies, I navigated to the Audit Policy folder. I checked the configuration for Audit logon events.

The assigned setting was Failure (meaning only failed login attempts are logged).

[Screenshot 03: Audit Logon Events setting](./screenshots/03-audit-logon-failure.png)

---

# Summary

This room emphasized that Incident Response begins long before an alert triggers. I learned about:

- Defining the Scope: The difference between a standard Event and a security Incident.
- Team Structure: The role of the CSIRT and the necessity of Chain of Custody.
- Readiness: The need for a "jump bag" of tools.
- Baselines: How to check existing Windows policies (Audit and Software Restrictions) to understand the current level of visibility and security before an incident occurs.