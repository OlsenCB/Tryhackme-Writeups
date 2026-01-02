# TryHackMe Room: Threat Hunting: Introduction

This room served as a foundational overview of the Threat Hunting discipline. Unlike standard Incident Response, which reacts to alerts, I learned that Threat Hunting is a proactive, intelligence-driven pursuit to find hidden threats that have evaded existing security controls. The room covered the core concepts, the mindset required, the hunting process, and the ultimate goals of reducing dwell time and improving security posture.

---

## Section: Core Concept

### Task 1: What do you call the approach to finding cyber security threats where there's an active effort done to look for signs of malicious activity?

As defined in the room, the proactive approach where security teams actively search for signs of malicious activity without waiting for an alert is called Threat hunting.

### Task 2: In this task, what are we contrasting threat hunting with?

The room establishes a clear distinction between the proactive nature of Threat Hunting and the reactive nature of Incident Response.

### Task 3: Incident response is innately reactive. What is done first thing when an initial notification or alert is received? (It is _______.)

In the Incident Response lifecycle, when an initial alert is received, it must first be triaged to determine its severity and validity.

### Task 4: Threat hunting is innately proactive. What is it guided by?

Since Threat Hunting doesn't rely on triggers or alerts, it is guided by Threat Intelligence to form hypotheses about potential compromises.

### Task 5: Threat Hunting and Incident Response are two different approaches that aim to ensure one specific goal is met. It is to strengthen the organisation's what?

Both disciplines, despite their different approaches, share the ultimate goal of strengthening the organization's security posture.

---

## Section: Threat Hunting Mindset

### Task 1: What is the most obvious and straightforward example of a Unique Threat Intelligence?

The most basic form of unique intelligence that hunters use to identify specific threats are Indicators of Compromise (IOCs), such as hashes, IPs, or domains.

---

## Section: Threat Hunting Process

### Task 1: Malwares are constantly being used in the toolkits of threat actors. What is the live malware repository that we touched upon above?

The room mentioned theZoo, a repository of live malware that researchers and hunters can use to analyze malicious code behavior in controlled environments.

### Task 2: Knowing what is normal in your environment and separating them from what's not is a skill all threat hunters should have. What example of Threat Intelligence blends well with environmental noise?

A major challenge in hunting is that Attack Residues often look very similar to normal system activity (environmental noise), requiring a deep understanding of the baseline to detect.

### Task 3: Threat actors are quite creative in finding vulnerabilities and misconfigurations. What should the organisation be extra vigilant in monitoring for announcements of?

Organizations must stay alert for announcements of zero-day vulnerabilities. When these are disclosed, hunters must immediately check if their assets are vulnerable and if there are historical traces of exploitation.

### Task 4: Characterisation of the subject of the hunt into specific and actionable identifiers is imperative for the hunt's success. How is it done most effectively?

To successfully find a threat, it must be characterized into actionable identifiers. This is most effectively done via Attack Signatures and IOCs.

---

## Section: Practical Application

### Task 1: Which tactic has the most techniques highlighted?

I analyzed the MITRE ATT&CK navigator layer provided in the task, which mapped out the techniques used by specific threat actors. The tactic column with the highest density of highlighted techniques was Discovery.

[Screenshot 01: MITRE ATT&CK Navigator showing Discovery tactic](./screenshots/01-mitre-discovery.png)

### Task 2: Which technique does the three threats have in common?

By comparing the highlighted techniques across the three different threat groups, I found that they all utilized Exploitation of Remote Services.

### Task 3: What technique does WannaCry and Conficker have in common?

Focusing specifically on WannaCry and Conficker, I identified that both malware families share the technique Inhibit System Recovery (often used to prevent victims from restoring backups).

### Task 4: What’s the score of techniques that Stuxnet and Conficker have in common?

I counted the number of overlapping techniques between Stuxnet and Conficker. The total score was 6.

---

## Section: Goals

### Task 1: What is the primary goal of Threat Hunting?

The overarching objective of proactive hunting is to identify threats that have slipped past defenses, thereby aiming to minimise a threat actor's dwell time.

### Task 2: Feedback is important to keep the organisation secure. Upon profiling threats through our Threat Hunting efforts, what should these profiles be translated to?

Once a hunt successfully identifies a new threat or behavior, the final step is to translate these findings into automated detection mechanisms to prevent future unnoticed compromises.

---

# Summary

This room laid the theoretical groundwork for the Threat Hunting module. I learned:

- Proactive vs. Reactive: The key difference between Hunting (looking for the unknown) and Incident Response (reacting to the known).
- The Process: How hunting starts with a hypothesis derived from Threat Intelligence and relies on distinguishing "normal" from "abnormal" (finding attack residues).
- The Goal: That the ultimate measure of success is reducing dwell time and converting manual hunts into automated detections to permanently improve the security posture.
- MITRE ATT&CK: How to map real-world threat actors to specific tactics and techniques to guide hunting efforts.