# TryHackMe Room: Eradication and Remediation

This room focuses on the critical, often high-pressure phases of Incident Response: Eradication (removing the threat) and Remediation (fixing the vulnerability). I explored the risks of premature action, the different eradication techniques (from automated cleanup to full rebuilds), and the importance of a solid recovery strategy to improve security posture and prevent re-infection.

---

## Section: Considerations

### Task 1: What is it that may cause an attacker to think that you already have a complex and detailed eradication plan in motion?

Acting too quickly without a complete picture is dangerous. Premature eradication can tip off the attacker, causing them to speed up their data exfiltration, destroy systems, or dig in deeper with more persistence mechanisms before you are ready to stop them completely.

### Task 2: What is an informal term used to describe the cycle wherein you keep discovering and identifying bad, eradicating it, finding it elsewhere, and doing it all over again?

If you don't scope the incident properly, you end up in a whack-a-mole cycle: finding malware, deleting it, only to find it popping up somewhere else because you didn't find the root cause or all the backdoors.

### Task 3: Of the two main goals of this phase, what is the first one?

The primary goal of this phase is twofold, but it starts with the obvious: Eradicate the bad guys. This involves prioritizing critical systems and deciding the best way to remove the threat.

---

## Section: Eradication Techniques

### Task 1: What technique is most effective on less sophisticated threats that employ well-known malicious tooling?

For common, non-advanced threats (commodity malware), tools like Antivirus and EDR can handle the job via Automated Eradication.

### Task 2: What technique is the most straightforward way to eradicate attacker traces?

The most nuclear but effective option is a Complete System Rebuild. Wiping the machine and starting over ensures no traces are left behind.

### Task 3: What downside does the complete system rebuild technique have? This approach entails what for the system?

The cost of a complete rebuild is downtime. Critical services might be unavailable while the system is being reimaged and restored, which must be factored into the decision.

### Task 4: Success of a targeted system cleanup is heavily reliant on how well the what has been done?

You can't clean what you don't know about. Targeted cleanup only works if the Scoping phase was thorough and accurate.

---

## Section: Remediation

### Task 1: What should take place in conjunction with Eradication techniques in order for its effects to last? An effective what?

Removing the malware isn't enough; you have to fix the hole they got in through. This requires an effective Remediation and Recovery strategy.

### Task 2: What remediation step ensures only absolutely necessary communication takes place between computers and subnets?

To limit lateral movement and reduce the attack surface, organizations should implement strict Network Segmentation.

### Task 3: What do you call the principle that posits that a user account should have access to only the absolutely necessary pieces of data, applications, or resources?

Reviewing entitlements is crucial. Accounts should operate under the Principle of least privilege, having only the permissions strictly needed for their role.

---

## Section: Recovery

### Task 1: Changes done during the remediation phase are geared towards strengthening the what of the organization?

The ultimate goal of remediation is not just to return to normal, but to be better than before, strengthening the overall security posture.

### Task 2: What kind of tests should be employed to check if the remediation tactics actually work?

To verify that the fixes actually work and the organization is safe from similar attacks, security teams should employ penetration tests and attack simulations.

---

## Section: Targeted System Cleanup: Identification and Scoping, and Eradication Feedback Loop Exercise

### Task 1: Which account gave the threat actors a foothold on the server?

Based on the scenario provided in the room, the compromised account was swiftspend_admin.

### Task 2: What is the default password for the admin account of the Jenkins service?

I accessed the machine via SSH to investigate the Jenkins installation. The initial password is stored in a specific file.

cat /var/lib/jenkins/secrets/initialAdminPassword

[Screenshot 01: Initial Jenkins admin password](./screenshots/01-initial-admin-password.png)

### Task 3: What is the email address of the other account within the Jenkins service?

I navigated to the Jenkins user directory to investigate other accounts. I found a directory for infraadmin and examined its config.xml file to find the email address.

cat /var/lib/jenkins/users/infraadmin_*/config.xml

[Screenshot 02: Infraadmin email address from config.xml](./screenshots/02-infraadmin-email.png)

### Task 4: What is the command being invoked by the project found in the Jenkins dashboard?

I checked the configuration of the Jenkins jobs, specifically the BackUp job.

cat /var/lib/jenkins/jobs/BackUp/config.xml

At the end of the file, the <command> tag pointed to a script named backup.sh.

[Screenshot 03: Backup job command path](./screenshots/03-backup-job-command.png)

### Task 5: How many times has the project been run before?

Checking the build history or job status in the directory structure indicated that the project run count was 0.

### Task 6: You will find a suspicious IP address. Which country is it hosted in? (Use AbuseIPDB; answer as written)

To investigate the suspicious activity, I first examined the content of the backup.sh script found in Task 4.

cat /opt/scripts/backup.sh

[Screenshot 04: Content of malicious backup.sh](./screenshots/04-backup-sh-code.png)

The script used curl to interact with a domain. To find the IP associated with that domain, I checked the /etc/hosts file.

[Screenshot 05: Suspicious IP in etc/hosts](./screenshots/05-etc-hosts-ip.png)

I then checked the IP address using AbuseIPDB, which identified its location as the Russian Federation.

[Screenshot 06: AbuseIPDB result for suspicious IP](./screenshots/06-abuseipdb-result.png)

### Task 7: Based on the MITRE ATT&CK Matrix, which Tactic is being applied by the threat actor here?

The script was designed to send data out to an external server. This aligns with the Exfiltration tactic.

### Task 8: Based on the Lockheed Martin version of the cyber kill chain, in what phase is the threat actor already in on this server?

Since the attacker has successfully compromised the system and is attempting to exfiltrate data (executing their end goal), they are in the Action on Objectives phase.

---

# Summary

This room highlighted the difference between simply deleting malware and truly securing a network. I learned:

- Eradication Strategies: Choosing between targeted cleanup (faster, requires better scoping) and system rebuilding (slower, but 100% effective).
- The Whack-a-Mole Problem: Why incomplete scoping leads to recurring infections.
- Remediation & Recovery: The importance of network segmentation, least privilege, and validating fixes through penetration testing to ensure the organization emerges stronger after an incident.
- Practical Investigation: Investigating a compromised Jenkins server to identify the entry point, persistence mechanisms, and exfiltration scripts.