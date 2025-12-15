# TryHackMe Room: Intro to Detection Engineering

This room provided a fundamental introduction to the discipline of Detection Engineering, covering the core philosophy, common detection types, and key frameworks used to build robust and adaptive defense systems. I focused on understanding the different approaches to identifying malicious activity and how frameworks like the Pyramid of Pain guide defensive strategy.

---

## Section: What is Detection Engineering?

### Task 1: Which detection type focuses on misalignments within the current infrastructure?

The detection type that focuses on discovering deviations or weaknesses in security settings, policies, or baselines is Configuration detection.

### Task 2: Which detection approach involves building an asset or activity baseline profile for detection?

The approach that involves establishing a "normal" profile for an asset or user activity and flagging deviations from that norm is known as Modelling detection.

### Task 3: Which type of detection integrates with defensive playbooks?

The detection type that is designed to correlate multiple events into a comprehensive narrative, allowing for a structured and automated response, is Threat Behaviour detection.

---

## Section: Detection Engineering Frameworks 1

### Task 1: Which framework looks at how to make it difficult for an adversary to change their approach when detected?

The framework that visualizes the effort required by an adversary to circumvent detection, emphasizing the most painful indicators to lose (like TTPs), is the Pyramid of Pain.

### Task 2: What is the improved Cyber Kill Chain framework called?

The evolution of Lockheed Martin's Cyber Kill Chain, which expands the focus to encompass internal attacker actions, is called The Unified Kill Chain.

### Task 3: How many phases are in the improved kill chain?

The Unified Kill Chain integrates elements of the MITRE ATT&CK framework and is composed of 18 distinct phases.

---

## Section: Detection Detective

### Task 1: What is the flag?

This section required me to apply the concepts learned by analyzing a detection strategy prompt and answering multiple choice questions that contribute to a final flag.

Q1: What categorisation class would you assign to the strategy?

The strategy focuses on monitoring Active Directory (AD) group changes, which is a key method for an attacker to escalate privileges or establish persistence.

Answer: Account Manipulation


Q2: What would be part of the Strategy Abstract?

The abstract defines the necessary data source. Since the strategy is about AD changes, the requirement is to monitor the relevant logs.

Answer: Collect Windows Event Logs related to AD group changes.


Q3: What would be part of the response plan?

The response plan defines the immediate steps for incident handling, which involves forensic validation of the suspicious activity.

Answer: Validate the group modified, user added and the user making the change.

The combined correct answers yielded the final flag.

[Screenshot 01: Final flag derived from detection strategy questions](./screenshots/01-final-flag.png)

---

# Summary

This introductory room successfully established the foundational principles of Detection Engineering. I learned to:

-Categorize Detections: Differentiate between Configuration, Modelling, and Threat Behaviour detection types.
-Understand Frameworks: Recognize the strategic value of the Pyramid of Pain and the comprehensive structure of The Unified Kill Chain.
-Apply Strategy: Practice the initial steps of the detection engineering lifecycle by defining the categorization, required data source, and response plan for a high-priority threat (Account Manipulation).

This room confirms that detection engineering is a proactive discipline focused on closing the loop between threat intelligence and defensive capabilities.