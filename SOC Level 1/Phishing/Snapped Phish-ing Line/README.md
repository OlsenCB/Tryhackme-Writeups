\# TryHackMe Room: Snapped Phish-ing Line



This room was a deep dive into analyzing a phishing attack from multiple angles. I started by examining a compromised email and then followed the trail to investigate the phishing kit, its associated infrastructure, and the data it collected. This involved using a mix of on-box tools and external intelligence services.



---



\### Task 1: Who is the individual who received an email attachment containing a PDF?



To quickly find the email with the .pdf attachment, I used the grep command on all the email files in the phish-emails directory.



grep ".pdf" ./phish-emails/\*



\[Screenshot 01: PDF attachment](./screenshots/01-pdf-attachment.png)



The output showed that only one email contained a PDF. I then used the provided credentials to log into Thunderbird and locate the email sent to that individual.



\[Screenshot 02: Found email in Thunderbird](./screenshots/02-email-found.png)



\### Task 2: What email address was used by the adversary to send the phishing emails?



The answer to this question was visible directly from the email found in the previous task's screenshot.



\### Task 3: What is the redirection URL to the phishing page for the individual Zoe Duncan? (defanged format)



I downloaded the email attachment and used the cat command to view its contents, revealing a URL.



\[Screenshot 03: Phishing redirection URL](./screenshots/03-phishing-url.png)



Next, I took the URL and pasted it into CyberChef to "defang" it, making it safe to share and non-clickable.



\[Screenshot 04: Defanged URL in CyberChef](./screenshots/04-cyberchef-defanged.png)



\### Task 4: What is the URL to the .zip archive of the phishing kit? (defanged format)



I used the cat /etc/hosts command to view the DNS mapping on the machine.



\[Screenshot 05: /etc/hosts file](./screenshots/05-etc-hosts.png)



I noticed an IP address mapped to a domain, and when I visited it, I found a page with a /data directory.



\[Screenshot 06: /data directory](./screenshots/06-data-directory.png)



I clicked on the .zip file, copied its link, and then used CyberChef again to defang the URL.



\[Screenshot 07: Second defanged URL in CyberChef](./screenshots/07-second-defanged-url.png)



\### Task 5: What is the SHA256 hash of the phishing kit archive?



I downloaded the Update365.zip file and used the sha256sum command on it to get its hash value.



sha256sum Update365.zip



\[Screenshot 08: SHA256 hash of zip file](./screenshots/08-zip-sha256.png)



\### Task 6: When was the phishing kit archive first submitted? (format: YYYY-MM-DD HH:MM:SS UTC)



To find this information, I went to VirusTotal and searched using the SHA256 hash from the previous task. The "First Submitted" date was located in the Details section.



\[Screenshot 09: VirusTotal first submission date](./screenshots/09-vt-first-submission.png)



\### Task 7: When was the SSL certificate the phishing domain used to host the phishing kit archive first logged? (format: YYYY-MM-DD)



After some searching, I decided to use the hint provided on the room's page. The hint revealed the date, as the SSL certificate was already expired.



\[Screenshot 10: SSL certificate hint](./screenshots/10-ssl-hint.png)



\### Task 8: What was the email address of the user who submitted their password twice?



I returned to the website and found a log.txt file inside the Update365 folder. I downloaded it using wget and then used grep to find all entries containing "Email", sorted them, and counted the unique occurrences to find the email address that appeared twice.



grep -i Email log.txt | sort | uniq -c



\[Screenshot 11: Duplicate email entries](./screenshots/11-duplicate-emails.png)



\### Task 9: What was the email address used by the adversary to collect compromised credentials?



I unzipped the downloaded phishing kit archive. By inspecting the files, I found a script named submit.php that contained the adversary's email address in a variable.



\[Screenshot 12: Adversary email in submit.php](./screenshots/12-submit-php.png)



\### Task 10: The adversary used other email addresses in the obtained phishing kit. What is the email address that ends in "@gmail.com"?



I used the grep command to search for the keyword "gmail" within all the files in the directory. This quickly revealed another email address.



grep gmail ./\*



\[Screenshot 13: Gmail address in files](./screenshots/13-gmail-address.png)



\### Task 11: What is the hidden flag?



The final challenge was to find a hidden flag. I returned to the website and decided to try some simple directory "fuzzing" by guessing common file names. After appending flag.txt to the URL, I unexpectedly found the flag.



\[Screenshot 14: Found flag on website](./screenshots/14-flag-found.png)



The flag was encoded in Base64, so I used the echo and base64 -d commands in the terminal to decode it.



echo "fUxSVV8zSHRfaFQxd195NExwe01IVAo=" | base64 -d

echo "fUxSVV8zSHRfaFQxd1d5NExwe01IVAo=" | base64 -d | rev



\[Screenshot 15: Decoded flag in terminal](./screenshots/15-decoded-flag.png)



---



\## Summary

In this room, I practiced:



Using command-line tools like grep, cat, and sha256sum to analyze files and extract key information.



Investigating email attachments to uncover redirection URLs.



Using CyberChef to defang malicious URLs and domains.



Correlating information from different sources, such as DNS records (/etc/hosts) and web pages.



Performing web reconnaissance to discover hidden files and directories.



Analyzing a phishing kit by inspecting its contents and scripts.



Using VirusTotal to gain intelligence on a malicious file.



Decoding Base64-encoded strings to reveal hidden data.



This room provided an excellent, practical walkthrough of a phishing incident investigation, combining both manual and automated analysis techniques.

