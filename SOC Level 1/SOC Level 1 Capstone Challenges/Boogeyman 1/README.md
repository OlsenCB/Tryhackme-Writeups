\# TryHackMe Room: Boogeyman 1



This room was a comprehensive incident response challenge that simulated a multi-stage attack, from an initial phishing email to a final data exfiltration. I played the role of a cybersecurity analyst, using various tools to perform email analysis, endpoint forensics, and network traffic analysis to uncover the attacker's actions and motives.



---



\## Section: \[Email Analysis] Look at that headers!



\### Task 1: What is the email address used to send the phishing email?



My first step was to analyze the phishing email itself. I opened the dump.eml file located in the Desktop/artefacts folder using Thunderbird. The sender's email address was visible in the "From" field.



\[Screenshot 01: Phishing email source and victim](./screenshots/01-email-info.png)



\### Task 2: What is the email address of the victim?



The victim's email address was also clearly visible in the email's header, in the "To" field.



\### Task 3: What is the name of the third-party mail relay service used by the attacker based on the DKIM-Signature and List-Unsubscribe headers?



To get more technical details about the email, I viewed the full source. The DKIM-Signature header provided the domain of the third-party mail relay service used by the attacker.



\[Screenshot 02: Third-party mail relay service](./screenshots/02-mail-relay.png)



\### Task 4: What is the name of the file inside the encrypted attachment?



The email contained a password-protected zip file. I opened the attachment and saw that it contained a file with a .lnk extension.



\[Screenshot 03: File inside encrypted attachment](./screenshots/03-encrypted-file.png)



\### Task 5: What is the password of the encrypted attachment?



The password for the encrypted zip attachment was easily found by reading the email's body text.



\### Task 6: Based on the result of the lnkparse tool, what is the encoded payload found in the Command Line Arguments field?



To analyze the malicious LNK file, I used the lnkparse tool on it. The output revealed a very long, encoded payload in the Command Line Arguments field, which was the answer.



\[Screenshot 04: Encoded payload from LNK file](./screenshots/04-lnk-payload.png)



---



\## Section: \[Endpoint Security] Are you sure that's an invoice?



\### Task 1: What are the domains used by the attacker for file hosting and C2? Provide the domains in alphabetical order. (e.g. a.domain.com,b.domain.com)



The encoded payload from the previous task appeared to be Base64-encoded. I used the base64 -d command to decode it, revealing the first domain and a PowerShell script.



\[Screenshot 05: First domain from base64 decoding](./screenshots/05-first-domain.png)



Now to find the second domain. The PowerShell script was logged in the powershell.json file. To analyze this JSON-formatted log, I used a command-line JSON parsing tool called jq. I filtered for the ScriptBlockText field and removed duplicates to find the unique commands executed, including the second domain.



cat powershell.json | jq -s -c 'sort\_by(.Timestamp) | .\[] | {ScriptBlockText}' | sort | uniq



\[Screenshot 06: Second domain from PowerShell logs](./screenshots/06-second-domain.png)



\### Task 2: What is the name of the enumeration tool downloaded by the attacker?



I examined the ScriptBlockText output from the previous task and found a GitHub URL. The name of the downloaded program in the URL path, which had a .ps1 extension, was the name of the tool.



\[Screenshot 07: Name of enumeration tool](./screenshots/07-enum-tool.png)



\### Task 3: What is the file accessed by the attacker using the downloaded sq3.exe binary? Provide the full file path with escaped backslashes.



I filtered the logs to find the script block associated with the sq3.exe binary.



cat powershell.json | jq '{ScriptBlockText}' | grep "sq3.exe"



\[Screenshot 08: File accessed by sq3.exe](./screenshots/08-sq3-exe-file.png)



The output showed a relative path. To find the full path, I searched the logs for the cd command to determine the user's working directory at the time the command was executed.



cat powershell.json | jq '{ScriptBlockText}' | grep "cd"



\[Screenshot 09: Full file path with username](./screenshots/09-full-file-path.png)





\### Task 4: What is the software that uses the file in Q3?



The file path I found in the previous task clearly indicated that the file was used by Microsoft Sticky Notes.



\### Task 5: What is the name of the exfiltrated file?



Returning to the decoded payload from Task 1, I saw the name of the file that was targeted for exfiltration.



\[Screenshot 10: Name of the exfiltrated file](./screenshots/10-exfiltrated-file.png)



\### Task 6: What type of file uses the .kdbx file extension?



A quick search revealed that the .kdbx file extension is used by KeePass, a popular password manager.



\### Task 7: What is the encoding used during the exfiltration attempt of the sensitive file?



The PowerShell script used a variable named $split to manipulate the exfiltrated data. The commands associated with this variable showed that the data was being encoded using hex.



\### Task 8: What is the tool used for exfiltration?



At the end of the PowerShell script, I found the command used to exfiltrate the data. The tool was nslookup.



---



\## Section: \[Network Traffic Analysis] They got us. Call the bank immediately!



\### Task 1: What software is used by the attacker to host its presumed file/payload server?



To analyze the network traffic, I opened the provided pcap file in Wireshark. I filtered the traffic by the C2 IP address I had previously identified: 167.71.211.113. To narrow it down further, I applied a filter for HTTP traffic.



\[Screenshot 11: Wireshark HTTP filter](./screenshots/11-wireshark-http-filter.png)



I followed an HTTP stream to inspect the server's response headers, which revealed that the server was running on Python.



\[Screenshot 12: Python server banner](./screenshots/12-python-server.png)



\### Task 2: What HTTP method is used by the C2 for the output of the commands executed by the attacker?



I looked back at the decoded payload from the endpoint analysis section. The script clearly showed that it used the -Method of POST for its C2 communications.



\[Screenshot 13: HTTP method POST](./screenshots/13-http-method-post.png)



\### Task 3: What is the protocol used during the exfiltration activity?



Since the attacker used nslookup for exfiltration, the protocol used was DNS. nslookup is a network administration command-line tool used to query the Domain Name System (DNS) to obtain domain name or IP address mapping or other DNS records. By abusing this tool, the attacker could encode and transfer data through DNS queries, a technique known as DNS tunneling.



\### Task 4: What is the password of the exfiltrated file?



I used Wireshark's search function (Ctrl+F) to find a packet containing sq3.exe. I then filtered for POST requests after that packet and followed the stream of the first result.



http.request.method==POST \&\& frame.number > 44459



\[Screenshot 14: Packet with credit card info](./screenshots/14-packet-with-info.png)



The resulting stream was a long, encoded string. I copied it and used CyberChef to decode it, which gave me the master password.



\[Screenshot 15: Decoded password in CyberChef](./screenshots/15-cyberchef-password.png)



\### Task 5: What is the credit card number stored inside the exfiltrated file?



This task required me to reconstruct the full exfiltrated file. I filtered the DNS traffic for A record queries (dns.qry.type==1) directed at the attacker's IP. The dns.qry.type==1 filter looks for DNS queries that ask for an IPv4 address, which is the type of query used for this form of data exfiltration.



I exported the filtered packets as a plain text file. Then, I used a command-line pipeline to extract the hex-encoded data and convert it into a binary file.



grep -Eo '\[0-9a-fA-F]{8,}.\[a-zA-Z0-9.-]+.\[a-zA-Z]{2,}' dnspackets.txt | sed 's/.\[a-zA-Z0-9.-]+.\[a-zA-Z]{2,}.\*//' | uniq | tr -d '\\n' > hexdump.txt

xxd -r -p hexdump.txt > base.kdbx



Finally, I opened the base.kdbx file, which was a KeePass database. I used the master password I found in the previous task to unlock it and reveal the contents, including the credit card number.



\[Screenshot 16: Final credit card number](./screenshots/16-credit-card-number.png)



---



\## Summary

This room was a fantastic exercise in full-spectrum incident response. I learned how to piece together an attack chain from start to finish by combining different skill sets:



Email Analysis: I used Thunderbird to inspect email headers and extract key information about the attacker's infrastructure.



Endpoint Forensics: I used command-line tools like base64, lnkparse, and jq to analyze endpoint artifacts and reconstruct the malicious PowerShell script.



Network Traffic Analysis: I used Wireshark to inspect network packets, identify C2 communication, and even reconstruct exfiltrated data through DNS tunneling.



This comprehensive approach is essential for any cybersecurity professional, as it highlights how different pieces of evidence fit together to tell the full story of an attack.















