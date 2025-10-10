# TryHackMe Room: Trooper

In this room we investigate the activities of **APT X (Tropic Trooper)** using reports, **OpenCTI**, and **MITRE ATT&CK Navigator**. Below are the tasks and how I solved them.

---

## 1. What kind of phishing campaign does APT X use as part of their TTPs?
The answer can be found directly in the report, in the very first paragraph.  

[Screenshot 01: First paragraph of the report](./screenshots/01-phishing-campaign.png)

---

## 2. What is the name of the malware used by APT X?
This is located at the end of the first page of the report, written in **bold**.  

[Screenshot 02: Malware name in report](./screenshots/02-malware-name.png)

---

## 3. What is the malware's STIX ID?
To find this, I logged into the machine provided in the task and accessed OpenCTI using the given credentials.  

[Screenshot 03a: OpenCTI login credentials](./screenshots/03a-opencti-credentials.png)

After logging in, I searched for the malware name (**USBferry**). Clicking on the malware entry shows its details, including the STIX ID.  

[Screenshot 03b: STIX ID in OpenCTI](./screenshots/03b-stix-id.png)


---

## 4. With the use of a USB, what technique did APT X use for initial access?
I went to [MITRE ATT&CK](https://attack.mitre.org/), selected **Enterprise**, and searched for the keyword `air-gapped`.  
This returned a single relevant technique.  

[Screenshot 04: Technique for initial access](./screenshots/04-technique-initial-access.png)

---

## 5. What is the identity of APT X?
Back in OpenCTI, by checking the reports linked to **USBferry**, I found the group’s identity: **Tropic Trooper**.  

[Screenshot 05: APT identity in OpenCTI](./screenshots/05-identity.png)

---

## 6. On OpenCTI, how many Attack Pattern techniques are associated with the APT?
Searching for **Tropic Trooper** in OpenCTI and opening the **Knowledge** section displays the number of associated techniques in a green box.  

[Screenshot 06: Attack Patterns count](./screenshots/06-attack-patterns.png)

---

## 7. What is the name of the tool linked to the APT?
In the same Knowledge section, under **Arsenal → Tools**, I found the answer.  

[Screenshot 07: Linked tool](./screenshots/07-tools.png)

---

## 8. Load up the Navigator. What is the sub-technique used by the APT under Valid Accounts?
On MITRE ATT&CK, I searched for **Tropic Trooper**, then used `Ctrl + F` to locate **Valid Accounts**. The sub-technique is displayed after the colon.  

[Screenshot 08: Valid Accounts sub-technique](./screenshots/08-valid-accounts.png)

---

## 9. Under what Tactics does the technique above fall?
Clicking the **Local Accounts** technique shows the corresponding Tactic on the right side.  

[Screenshot 09: Tactic for Valid Accounts](./screenshots/09-tactic.png)

---

## 10. What technique is the group known for using under the tactic Collection?
Back in OpenCTI, within **Tropic Trooper → Arsenal → Attack Patterns**, I checked the **Collection** column to find the answer.  

[Screenshot 10: Collection technique](./screenshots/10-collection.png)

---

## Summary
In this room I practiced:
- Using **threat intelligence platforms (OpenCTI)** to query malware, groups, and STIX data  
- Navigating the **MITRE ATT&CK framework** to map techniques and tactics  
- Understanding how APT groups like **Tropic Trooper** operate (malware, tools, TTPs)  

This exercise reinforced how to connect intelligence sources to attacker behaviors and report them in a structured way.
