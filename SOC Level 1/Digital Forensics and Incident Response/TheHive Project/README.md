# TryHackMe Room: TheHive Project

This room was a practical introduction to TheHive, an open-source incident response platform. I learned how to use TheHive to manage and document security incidents, with a focus on its key features like case creation, observable analysis, and integration with other tools like Cortex and the MITRE ATT&CK framework.

---

## Section: TheHive Features & Integrations

### Task 1: Which open-source platform supports the analysis of observables within TheHive?

TheHive can be integrated with other platforms to perform automated analysis. I found the answer to this question in the "Observable Enrichment with Cortex" section of the room's materials.

[Screenshot 01: Observable Enrichment with Cortex](./screenshots/01-cortex-integration.png)
---

## Section: User Profiles & Permissions

### Task 1: Which pre-configured account cannot manage any cases?

By examining the user profiles, it was clear that the account which cannot manage any cases is the admin account.

[Screenshot 02: User profiles permissions](./screenshots/02-user-profiles.png)

### Task 2: Which permission allows a user to create, update or delete observables?

I looked at the permissions list to find the one that grants the ability to manage observables. The permission required to create, update, or delete observables is manageObservable.

[Screenshot 03: All user permissions](./screenshots/03-user-permissions.png)

### Task 3: Which permission allows a user to execute actions?

Using the same permissions list from the previous task, I found that the permission allowing a user to execute actions is manageAction.

---

## Section: Analyst Interface Navigation

### Task 1: Where are the TTPs imported from?

TheHive uses a well-known framework for categorizing Tactics, Techniques, and Procedures (TTPs). I learned that these TTPs are imported from the MITRE ATT&CK framework.

### Task 2: According to the Framework, what type of Detection "Data source" would our investigation be classified under?

I visited the MITRE ATT&CK website and navigated to the Data Sources section. Since the investigation involved FTP data exfiltration and a PCAP file, the most relevant data source was Network Traffic.

[Screenshot 04: MITRE data sources](./screenshots/04-mitre-data-source.png)

### Task 3: Upload the pcap file as an observable. What is the flag obtained from https://10.10.162.240//files/flag.html

First, I logged into TheHive with the provided credentials:

Username: analyst@tryhackme.me

Password: analyst1234

I then created a new case as instructed in the room. Next, I downloaded the provided pcap file and uploaded it to TheHive as an observable.

[Screenshot 05: Uploading observable](./screenshots/05-upload-observable.png)

The file analysis revealed a URL. After changing the protocol from https to http, I navigated to the page to find the flag.

[Screenshot 06: Final flag](./screenshots/06-final-flag.png)

---

## Summary
This room was a great introduction to TheHive, a powerful platform for Security Operations and incident response. I gained hands-on experience by:

Creating a new case and managing its details.

Understanding different user profiles and permissions.

Utilizing a threat intelligence framework like MITRE ATT&CK to contextualize an investigation.

Adding observables to a case and analyzing them.

Correlating data from a packet capture (pcap) to find a hidden flag.

This experience provided a solid foundation for understanding how a SOAR (Security Orchestration, Automation, and Response) platform works in a real-world scenario.