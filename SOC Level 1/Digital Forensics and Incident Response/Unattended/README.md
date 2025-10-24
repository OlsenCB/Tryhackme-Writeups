\# TryHackMe Room: Unattended



This room presented a scenario where a user, THM-RFedora, had executed suspicious actions on a Windows machine. My task was to perform a digital forensic investigation to uncover these actions. Using a forensic image collected by KAPE, I relied on tools like Registry Explorer, Autopsy, and JLECmd to analyze various artifacts and piece together a clear timeline of events.



---



\## Section: Snooping around



\### Task 1: What file type was searched for using the search bar in Windows Explorer?



I began the investigation by opening the Registry Explorer tool. I navigated to the user's registry hive file, NTUSER.DAT, within the provided KAPE results. I then followed the path Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\WordWheelQuery and selected MRULISTex. By using the Data Interpreter, I was able to see the recent searches made in Windows Explorer, which revealed the file type.



\[Screenshot 01: Searched file extension](./screenshots/01-file-extension.png)



\### Task 2: What top-secret keyword was searched for using the search bar in Windows Explorer?



The next entry in the WordWheelQuery key, just below the previous one, revealed a keyword that was searched for.



\[Screenshot 02: Top-secret keyword search](./screenshots/02-top-secret-keyword.png)



---



\## Section: Can't simply open it



\### Task 1: What is the name of the downloaded file to the Downloads folder?



Next, I opened Autopsy and added the KAPE results directory as a data source. I went to the Data Artifacts section and selected Web Downloads. The list showed the name of a downloaded executable file.



\[Screenshot 03: Downloaded executable file](./screenshots/03-downloaded-exe.png)



\### Task 2: When was the file from the previous question downloaded? (YYYY-MM-DD HH:MM:SS UTC)



The timestamp for the file's download was visible on the same screen, in the Date Accessed column.



\### Task 3: Thanks to the previously downloaded file, a PNG file was opened. When was this file opened? (YYYY-MM-DD HH:MM:SS)



Returning to Registry Explorer, I navigated to the RecentDocs key, which tracks recently opened documents. I looked specifically for a PNG file, and using the Data Interpreter on its corresponding entry, I found the exact date and time it was opened.



\[Screenshot 04: PNG file open timestamp](./screenshots/04-png-open-time.png)



---



\## Section: Sending it outside



Task 1: A text file was created in the Desktop folder. How many times was this file opened?



To analyze user activity related to specific files, I used JLECmd.exe, a tool for parsing Jump Lists. I pointed the tool to the user's profile directory. The output contained an entry for a text file created on the desktop. The Interaction Count field showed how many times the file was accessed.



JLECmd.exe -d C:\\Users\\THM-RFedora\\Desktop\\kape-results\\C



\[Screenshot 05: Text file interaction count](./screenshots/05-txt-file-count.png)



\### Task 2: When was the text file from the previous question last modified? (MM/DD/YYYY HH:MM)



The Last Modified timestamp was visible in the output from the JLECmd command, on the same line as the file entry.



\### Task 3: The contents of the file were exfiltrated to pastebin.com. What is the generated URL of the exfiltrated data?



I returned to Autopsy to analyze the user's web history. Using the Keyword Search function, I looked for pastebin.com. The search results included the specific URL used for the exfiltration.



\[Screenshot 06: Pastebin exfiltration URL](./screenshots/06-pastebin-url.png)



\### Task 4: What is the string that was copied to the pastebin URL?



The content of the exfiltrated string was visible in the URL found in the previous task.



---



\## Summary

This room was an excellent exercise in user activity analysis on a Windows machine. I learned how to:



Leverage KAPE results and forensic tools to analyze key Windows artifacts.



Examine the Windows Registry to uncover user searches and recently accessed documents.



Utilize Autopsy to analyze web history and file downloads.



Use JLECmd to parse Jump Lists and see file access counts.



Correlate findings from different data sources to reconstruct a user's actions, from searching for a file to downloading it, and finally exfiltrating its contents.

