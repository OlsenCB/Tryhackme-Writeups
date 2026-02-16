# TryHackMe Room: Identification & Scoping

This room focuses on the first two critical phases of the Incident Response lifecycle: Identification (detecting that an incident has occurred) and Scoping (determining the extent of the incident). I acted as an Incident Responder, analyzing email threads, asset inventories, and log files to triage tickets and uncover the full scale of a phishing attack.

---

## Section: Identification: Unearthing the Existence of a Security Incident

### Task 1: What is the Subject of Ticket#2023012398704232?

I started by examining the Outlook email inbox on the provided VM. I opened the first email in the thread and scrolled down to the ticket details table. The "Ticket Subject" was clearly listed there.

[Screenshot 01: Ticket Subject in email table](./screenshots/01-ticket-subject.png)

### Task 2: According to your colleague John, the issue outlined on Ticket#2023012398704232 could be related to what?

I read the follow-up email from my colleague John (located directly below the initial ticket email). He suggested that the reported issue might be related to a specific type of threat.

[Screenshot 02: Colleague's assessment of the issue](./screenshots/02-colleague-assessment.png)

### Task 3: Your colleague requested what kind of data pertaining to the machine WKSTN-02?

In the forwarded section of the email thread, I found a request for specific logs to help with the investigation of the workstation WKSTN-02. The request was for Web Proxy logs.

[Screenshot 03: Request for Web Proxy logs](./screenshots/03-web-proxy-logs.png)

---

## Section: Scoping: Understanding the Extent of a Security Incident

### Task 1: Based on Ticket#2023012398704231 and Asset Inventory shown in this task, who owns the computer that needs Endpoint Protection definitions updated?

First, I checked the email associated with the ticket to identify the IP address of the affected computer: 172.16.1.153.

[Screenshot 04: Affected IP address in ticket](./screenshots/04-affected-ip.png)

Next, I cross-referenced this IP address with the Asset Inventory table provided in the task instructions. The inventory showed that this IP belongs to Derick Marshall.

[Screenshot 05: Derick Marshall in Asset Inventory](./screenshots/05-derick-marshall.png)

### Task 2: Based on the email exchanges and SoD shown in this task, what was the phishing domain where the compromised credentials in Ticket#2023012398704232 were submitted?

I consulted the Scope of Discovery (SoD) table in the task description. I looked for the entry where the "Threat type" was listed as "Phishing Domain" and the source matched the ticket number.

[Screenshot 06: Phishing domain in SoD table](./screenshots/06-sod-phishing-domain.png)

### Task 3: Based on Ticket#2023012398704233, what phishing domain should be added to the SoD?

I found the email containing Ticket #...33. In the "Detailed description" section of the ticket, a URL was mentioned. To block this threat, the domain kennaroads.buzz needs to be added to the scope.

[Screenshot 07: New phishing domain kennaroads.buzz](./screenshots/07-kennaroads-domain.png)

---
## Section: Identification and Scoping Feedback Loop: An Intelligence-Driven Incident Response Process

### Task 1: Concerning Ticket#2023012398704232 and according to your colleague John, what domain should be added to the SoD since it was used for email spoofing?

I went back to the email thread for ticket #...32. In a message just above the previous one, John explicitly mentioned a domain used for the spoofing attack that needs to be blocked.

The domain is emkei.cz.

[Screenshot 08: Spoofing domain emkei.cz](./screenshots/08-emkei-domain.png)

### Task 2: Concerning the available artefacts gathered for analysis of Ticket#2023012398704232, who is the other user that received a similar phishing email but did not open a ticket nor report the issue?

I found an email report regarding a failed delivery/exchange error stating that the user alexander.swift@swiftspend.finance does not exist on the exchange. This indicates this user was also targeted.

[Screenshot 09: Targeted user Alexander Swift](./screenshots/09-alexander-swift.png)

### Task 3: Concerning Ticket#2023012398704232, what additional IoC could be added to the SoD and be used as a pivot point for discovery?

I opened the attached file named MessageTraceSearchResults.csv (or .xlsx) to analyze the email flow. Filtering the results, I identified the sender address responsible for distributing the phishing emails.

The IoC is sales.tal0nix@gmail.com.

[Screenshot 10: Attacker email address from Message Trace](./screenshots/10-attacker-email.png)

### Task 4: Based on the email exchanges and attachments in those exchanges, what is the password of the compromised user?

I opened the second attached file, which contained the Web Proxy logs (URL history). I scanned the URLs for suspicious activity. I found a GET request where the user's credentials (email and password) were passed in cleartext within the URL parameters.

[Screenshot 11: Compromised password in URL logs](./screenshots/11-compromised-password.png)

---

# Summary

This room demonstrated the importance of the Identification and Scoping phases. I practiced:

- Triage: Extracting key information from help desk tickets and email threads.
- Correlation: Mapping IP addresses to users using Asset Inventories.
- Scope Definition: Building a Scope of Discovery (SoD) list by identifying IoCs (Domains, IPs, Email addresses).
- Forensic Analysis: Digging into CSV attachments (Message Traces and Proxy Logs) to find pivot points and compromised credentials.