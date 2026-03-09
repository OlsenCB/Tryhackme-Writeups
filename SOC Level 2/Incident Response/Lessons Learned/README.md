# TryHackMe Room: Lessons Learned

This room wraps up the entire SwiftSpend Incident Response scenario. It focuses on the final, crucial phase of the IR lifecycle: Lessons Learned. Here, I explored how to consolidate findings, draft Executive and Technical summaries, and transform intelligence into actionable detection rules for the future.

---

## Section: Brewing it All Together

### Task 1: What phase of the IR process focuses on people, documentations, and technological capabilities?

This phase emphasizes establishing a response capability before an incident occurs. It involves training people, preparing documentation, and ensuring technological readiness.

Answer: Preparation

### Task 2: What phase of the IR process is reliant on the effectivity and synergy of all the other phases?

The success of fixing the issues (Remediation) relies heavily on how well the previous phases (Identification, Scoping, Eradication) were handled. If scoping was poor, remediation will fail (the "whack-a-mole" effect).

Answer: Eradication, Remediation, and Recovery

---

## Section: The SwiftSpend Incident Recap

### Task 1: What malicious file type has been found with two different versions?

Reviewing the incident summary, we identified that the attacker used two different versions of a specific malicious payload type to gain access.

Answer: Dropper

### Task 2: What's the name of the malicious file found in the Jenkins server?

During the investigation of the Jenkins platform compromise, we discovered a scheduled exfiltration script disguised as a backup implementation.

Answer: backup.sh

---

## Section: The Executive and Technical Summaries

### Task 1: What are the two technical details removed in the revised paragraph?

I opened the provided VM to view the draft report. Comparing the original draft with the streamlined Executive Summary in the task description, I noticed that specific details about who sent the email and the specific ticket ID were removed to keep the summary high-level for executives.

[Screenshot 01: Draft report showing sender and ticket ID](./screenshots/01-draft-report.png)

Answer: sales.tal0nix@gmail.com, Ticket#2023012398704232

### Task 2: What technical detail in the fifth paragraph of the draft can be removed?

In the fifth paragraph of the draft, there was a list of specific email security protocols that were too technical for an executive audience.

[Screenshot 02: Technical details (SPF/DKIM) in paragraph 5](./screenshots/02-technical-details-p5.png)

Answer: (i.e., SPF, DKIM, DMARC)

### Task 3: In the draft, the third paragraph talks about the discovery of numerous artefacts via the review of a packet capture. What are those artefacts?

I referred to the artifacts.xlsx file (specifically the Scope of Discovery tab) provided in the VM. The third paragraph refers to the network analysis (Wireshark) from previous rooms, which revealed a specific C2 IP address and a malicious executable.

[Screenshot 03: Artifacts IP and Dropper file](./screenshots/03-artifacts-ip-dropper.png)

Answer: 167.71.198.43, dropper.exe

### Task 4: How did the investigators find out about this? (The answer is the entire string as described in the SoD)

Staying in the Excel sheet, I looked at the entry regarding the hijacked SwiftSpend domain hosting the attacker's IP. I checked the "Source" column to see how this was discovered.

[Screenshot 04: Source column showing pivoting from backup.sh](./screenshots/04-sod-source.png)

Answer: Pivoting from backup.sh

---

## Section: Unique Threat Intelligence

### Task 1: What did we use to transform IOCs as detection rules in a vendor-agnostic format?

To ensure our findings can be used across different SIEMs and tools, we use the industry-standard format for detection rules.

Answer: Sigma

### Task 2: In the Sigma Rule that we've created, what is the logsource category used by the author?

I examined the Sigma rule snippet provided in the task. Under the logsource section, the category was explicitly defined.

Answer: create_stream_hash

---

# Summary

This room concluded the Incident Response module by teaching the importance of the Post-Incident Activity phase. I learned:

- Reporting: How to differentiate between an Executive Summary (high-level, business impact) and a Technical Summary (detailed, artifact-focused).
- Documentation: The importance of maintaining a Scope of Discovery (SoD) document throughout the investigation.
- Future Proofing: How to convert IOCs gathered during the investigation into Sigma rules to detect similar threats in the future, effectively closing the loop and improving the organization's security posture.