# TryHackMe Room: Retracted

In this room, I practiced using **PowerShell** and **Sysmon** logs to perform a forensic analysis on a compromised Windows machine. The goal was to reconstruct a timeline of events and identify key indicators of compromise (IOCs).

---

## The Message

### 1. What is the full path of the text file containing the "message"?

After starting the machine, I found the `Sophie.txt` file on the desktop. The path to the file is the answer.
C:\Users\sophie\Desktop\Sophie.txt

[Screenshot 01: Path to Sophie.txt](./screenshots/01-sophie-path.png)

### 2. What program was used to create the text file?

As seen in the screenshot above, the file automatically opens with **Notepad**. This is a strong indicator of the program used to create it.
notepad.exe

### 3. What is the time of execution of the process that created the text file? Timezone UTC (Format YYYY-MM-DD hh:mm:ss)

To get this information, I needed to use PowerShell to search for a Sysmon event with **Event ID 1** that contains the file name `sophie.txt`.

(Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' | Where-Object {($_.Message -like "*sophie.txt*")}).TimeCreated

[Screenshot 02: Execution time of Sophie.txt](./screenshots/02-sophie-time.png)

Note: The second date in the output is the correct one, as the first one is the time when the room was launched by a user.

---

## Something Wrong

### 1. What is the filename of this "installer"? (Including the file extension

The task description states that the program was downloaded from Google. As people rarely change the default download location, I checked the Downloads folder. However, Sophie had a separate folder named download, where I found the file.

antivirus.exe

[Screenshot 03: The antivirus.exe file](./screenshots/03-antivirus.png)

### 2. What is the download location of this installer?

The location is visible in the screenshot above.

C:\Users\sophie\download

### 3. The installer encrypts files and then adds a file extension to the end of the file name. What is this file extension?

I went back to PowerShell to search the Sysmon logs. This time, I looked for Event ID 11, which indicates a file creation, and filtered for antivirus.exe.
Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' | Where-Object {($_.Id -eq "11") -and ($_.Message -like "*antivirus.exe*")} | Select-Object -First 1 | Select-Object *

[Screenshot 04: Sysmon Event ID 11 details](./screenshots/04-sysmon-event-11.png)

In the event details, under TargetFilename, I found a file named sample.xlsx.dmp, revealing the file extension.

### 4. The installer reached out to an IP. What is this IP?

I modified the previous command to search for Event ID 3, which logs network connections.
Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' | Where-Object {($_.Id -eq "3") -and ($_.Message -like "*antivirus.exe*")} | Select-Object *

[Screenshot 05: Sysmon Event ID 3 details](./screenshots/05-sysmon-event-3.png)

The output showed the destination IP for the connection.

---

## Back To Normal

### 1. The threat actor logged in via RDP right after the “installer” was downloaded. What is the source IP?

When solving this task, it's important to remember that multiple users connect to the same machine via RDP. To avoid logs from other users, I filtered the Sysmon logs for Event ID 3 and the keyword "RDP", limiting the results to the year 2024.

Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' | Where-Object {($_.Id -eq "3") -and ($_.Message -like "*RDP*") -and ($_.TimeCreated -like "*2024*")} | Select-Object -First 1 | Select-Object *

[Screenshot 06: RDP login event](./screenshots/06-rdp-login.png)

### 2. This other person downloaded a file and ran it. When was this file run? Timezone UTC (Format YYYY-MM-DD hh:mm:ss)

The file in question is the decryptor.exe, also found in the download folder. I used a similar command as in the first section to find its execution time.

(Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' | Where-Object {($_.Message -like "*decryptor.exe*")}).TimeCreated

[Screenshot 07: Execution time of decryptor.exe](./screenshots/07-decryptor-time.png)

---

## Doesn't Make Sense

This section required arranging a series of events into a logical timeline based on the story of the incident.

1. Sophie downloaded the malware and ran it.

2. The downloaded malware encrypted the files on the computer and showed a ransomware note.

3. After seeing the ransomware note, Sophie ran out and reached out to you for help.

4. While Sophie was away, an intruder logged into Sophie's machine via RDP and started looking around.

5. The intruder realized he infected a charity organization. He then downloaded a decryptor and decrypted all the files.

6. After all the files are restored, the intruder left the desktop telling Sophie to check her Bitcoin.

7. Sophie and I arrive on the scene to investigate. At this point, the intruder was gone.

---

## Summary
In this room, I practiced:

Using PowerShell to query and filter Windows event logs.

Identifying and analyzing Sysmon logs based on Event IDs (1, 3, and 11) to track file creation, network connections, and process execution.

Reconstructing a timeline of a security incident.

Identifying key artifacts such as file paths, program names, and IP addresses.

Working with a real-world scenario to piece together the narrative of an attack.

This room provided an excellent, practical walkthrough of a log analysis and incident response scenario.
