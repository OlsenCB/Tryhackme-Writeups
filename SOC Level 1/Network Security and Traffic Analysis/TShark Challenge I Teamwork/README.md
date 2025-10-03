#TryHackMe Room: TShark Challenge I: Teamwork



This room was a practical introduction to using TShark for network forensics and correlating findings with open-source intelligence. The challenge involved analyzing a pcap file to identify a phishing attempt, find its associated details, and verify them using VirusTotal.



---



\## TShark Investigation



\### 1.What is the full URL of the malicious/suspicious domain address?



Enter your answer in a defanged format.



To identify the malicious domain, I used tshark to read the pcap file, filtered the output for DNS queries, and then used awk and uniq to list the unique domains and their connection counts.



tshark -r teamwork.pcap -T fields -e dns.qry.name | awk NF | uniq -c



\[Screenshot 01: DNS query names](./screenshots/01-dns-qry-names.png)



The output showed a high number of connections to a domain that was attempting to impersonate PayPal. I then "defanged" the full URL by modifying it to be non-clickable and safe to share.



\### 2.When was the URL of the malicious/suspicious domain address first submitted to VirusTotal?



To get more information about the suspicious domain, I went to the VirusTotal website and searched for the URL I found in the previous task. The "First Submission" date was listed in the Details section.



\[Screenshot 02: VirusTotal first submission date](./screenshots/02-vt-submission-date.png)



\### 3. Which known service was the domain trying to impersonate?



By examining the URL found in the first task, it was clear that the domain was designed to impersonate PayPal.



\### 4. What is the IP address of the malicious domain?



Enter your answer in a defanged format.



Staying on the VirusTotal page, I navigated to the Relations section. Here, I found the IP address associated with the malicious domain.



\[Screenshot 03: VirusTotal malicious IP address](./screenshots/03-vt-malicious-ip.png)



I then converted the IP address to a defanged format by enclosing the dots in brackets.



\### What is the email address that was used?



Enter your answer in defanged format. (format: aaa\[at]bbb\[.]ccc)



To find the email address, I returned to the TShark machine. Since TShark's default output is not suitable for searching with grep, I first saved the verbose output to a text file. Then, I used grep with a regular expression to extract the email address from the file.



tshark -r teamwork.pcap -V > fullcapture.txt

grep -E -o "\[a-zA-Z0-9.\_%+-]+@\[a-zA-Z0-9.-]+.\[a-zA-Z]{2,}" fullcapture.txt > email\_add.txt

cat email\_add.txt



\[Screenshot 04: Email address from capture](./screenshots/04-email-address.png)



After retrieving the email address, I modified it to match the requested defanged format.



---



\## Summary

In this room, I practiced:



Using TShark to analyze network traffic and identify a malicious domain.



Employing TShark's verbose mode and grep with regular expressions to extract specific data, such as an email address.



Utilizing VirusTotal to gain external threat intelligence on a suspicious domain.



Correlating findings from a local pcap file with data from an online service.



Properly defanging URLs, IP addresses, and email addresses to safely report indicators of compromise.

