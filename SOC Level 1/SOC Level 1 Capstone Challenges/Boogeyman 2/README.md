\# TryHackMe Room: Boogeyman 2



This room was a fantastic continuation of the previous Boogeyman room, allowing me to dive deeper into the host's compromised state. Building on my initial network traffic analysis, this investigation focused on analyzing endpoint artifacts, including the malicious document and a memory dump, to understand the attacker's full TTPs (Tactics, Techniques, and Procedures).



---



\## Malware Analysis \& Host Forensics



\### Task 1: What email was used to send the phishing email?



I began the investigation by examining the dump.eml file located in the Artefacts folder on the desktop. Opening the email revealed the sender's address in the "From" field.



\[Screenshot 01: Sender and victim email addresses](./screenshots/01-email-addresses.png)



\### Task 2: What is the email of the victim employee?



The victim's email address was also clearly visible in the email's "To" field on the same screen.



\### Task 3: What is the name of the attached malicious document?



The malicious document was attached to the email, and its name was visible in the attachments section.



\### Task 4: What is the MD5 hash of the malicious attachment?



I downloaded the attached Word document and used the md5sum command in the terminal to get its hash, which is a key indicator of compromise.



md5sum Resume\_WesleyTaylor.doc



\[Screenshot 02: MD5 hash of the document](./screenshots/02-md5-hash.png)



\### Task 5: What URL is used to download the stage 2 payload based on the document's macro?



I used the olevba tool to scan the malicious Word document for embedded macros. The tool successfully identified a macro that was attempting to download a file from a suspicious URL.



olevba Resume\_WesleyTaylor.doc



\[Screenshot 03: Malicious macro URL from olevba](./screenshots/03-olevba-macro.png)



\### Task 6: What is the name of the process that executed the newly downloaded stage 2 payload?



The olevba output from the previous task showed the command that would be executed by the macro. The command used wscript.exe to run the second-stage payload.



\### Task 7: What is the full file path of the malicious stage 2 payload?



The full file path was also clearly visible in the olevba output: C:\\ProgramData\\update.js.



Task 8: What is the PID of the process that executed the stage 2 payload?



To get more details on the processes running on the compromised machine, I used the Volatility framework on the provided memory dump file. The windows.pstree plugin was perfect for viewing the process tree and finding the PID of wscript.exe.



vol -f WKSTN-2961.raw windows.pstree



\[Screenshot 04: PID and PPID of wscript.exe](./screenshots/04-wscript-pid-ppid.png)



\### Task 9: What is the parent PID of the process that executed the stage 2 payload?



The parent PID (PPID) of wscript.exe was also listed in the output from the windows.pstree command.



\### Task 10: What URL is used to download the malicious binary executed by the stage 2 payload?



The malicious JavaScript payload (update.js) was likely downloading another binary. To find the URL, I used the strings command on the raw memory dump and searched for the suspicious domain I found earlier.



strings WKSTN-2961.raw | grep boogeymanisback.lol



The output revealed a second URL pointing to an executable file.



\[Screenshot 05: Malicious binary URL from strings](./screenshots/05-strings-binary-url.png)



\### Task 11: What is the PID of the malicious process used to establish the C2 connection?



I used Volatility's windows.netscan plugin to inspect the network connections in the memory dump. I searched for a connection to the attacker's IP address and found that updater.exe was responsible, and its PID was clearly listed.



vol -f WKSTN-2961.raw windows.netscan



\[Screenshot 06: PID of the C2 process](./screenshots/06-c2-process-pid.png)



\### Task 12: What is the full file path of the malicious process used to establish the C2 connection?



Knowing the PID from the previous task, I used Volatility's windows.dlllist plugin to get more details about the process, including its full file path, which was at the very top of the output.



vol -f WKSTN-2961.raw windows.dlllist --pid 6216



\[Screenshot 07: Full file path from dlllist](./screenshots/07-dlllist-filepath.png)



\### Task 13: What is the IP address and port of the C2 connection initiated by the malicious binary? (Format: IP address:port)



The IP address and port were visible in the output of the windows.netscan command from Task 11.



\### Task 14: What is the full file path of the malicious email attachment based on the memory dump?



I used Volatility's windows.filescan plugin to find the full path of the malicious email attachment from the memory dump. I piped the output to grep to quickly find the file by its name.



vol -f WKSTN-2961.raw windows.filescan | grep Resume\_WesleyTaylor



\[Screenshot 08: Full path of the malicious attachment](./screenshots/08-filescan-filepath.png)



\### Task 15: The attacker implanted a scheduled task right after establishing the c2 callback. What is the full command used by the attacker to maintain persistent access?



To find the persistence mechanism, I searched the memory dump for schtasks, a command-line utility used to create scheduled tasks. The strings command with grep was perfect for this.



strings WKSTN-2961.raw | grep schtasks



The output revealed the full command used by the attacker to create a scheduled task for persistence.



\[Screenshot 09: Scheduled task command for persistence](./screenshots/09-schtasks-command.png)



---



\## Summary

This room was a powerful demonstration of advanced host forensics using the Volatility framework. I learned how to:



Analyze the initial attack vector by examining a malicious document's macros with olevba.



Use Volatility to analyze a host's memory dump, recreating the attack timeline by inspecting process relationships (windows.pstree).



Identify a malicious process's C2 communication by analyzing network connections (windows.netscan).



Uncover the attacker's persistence mechanism by searching for specific commands and artifacts (schtasks).



Combine information from various sources (email, document, memory dump) to build a complete picture of the compromise, from initial access to command and control.



This comprehensive approach is crucial for any security professional involved in threat hunting and incident response.

