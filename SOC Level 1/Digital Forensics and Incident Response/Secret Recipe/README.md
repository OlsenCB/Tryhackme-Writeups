# TryHackMe Room: Secret Recipe

This room was a practical deep dive into Windows Registry Forensics. The scenario provided a set of registry hives collected from a user's machine, and my goal was to analyze them to uncover a wide range of user activity. I used a forensic tool to examine different hives, revealing everything from network connections to recently executed commands and even the time a specific application was in focus.

---

## Section: Introduction

### Task 2: How many files are available in the Artifacts folder on the Desktop?

After launching the machine, I navigated to the Artifacts folder on the Desktop. I counted all the files inside.

[Screenshot 01: Count of files in Artifacts folder](./screenshots/01-artifacts-count.png)

---

## Section: Windows Registry Forensics

## Task 1: What is the computer name of the machine found in the registry?

To begin the forensic analysis, I opened Registry Explorer and loaded all the registry hives from the Artifacts folder. I then examined the SYSTEM hive, following the path SYSTEM\ControlSet001\Control\ComputerName\ComputerName to find the computer's name.

[Screenshot 02: Computer name from SYSTEM hive](./screenshots/02-computer-name.png)

### Task 2: When was the Administrator account created on this machine? (Format: yyyy-mm-dd hh:mm:ss)

To find information about user accounts, I navigated to the SAM hive. I went to the SAM\Domains\Account\Users key, where I found the creation time for the Administrator account.

[Screenshot 03: Administrator account creation time](./screenshots/03-administrator-creation.png)

### Task 3: What is the RID associated with the Administrator account?

Within the same Users key in the SAM hive, I could see the RID (Relative Identifier) associated with each user account, including the Administrator.

[Screenshot 04: Administrator RID from SAM hive](./screenshots/04-administrator-rid.png)

### Task 4: How many user accounts were observed on this machine?

By simply counting the accounts listed in the SAM\Domains\Account\Users key, I could determine the total number of user accounts on the machine.

### Task 5: There seems to be a suspicious account created as a backdoor with RID 1013. What is the account name?

While counting the user accounts, I noticed a suspicious one with a high RID number, which is often indicative of a manually created account or backdoor. The account with RID 1013 was named bdoor.

[Screenshot 05: Backdoor account name (bdoor)](./screenshots/05-backdoor-account.png)

### Task 6: What is the VPN connection this host connected to?

Next, I analyzed the SOFTWARE hive to find information about network connections. By navigating to SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Unmanaged, I found a key that indicated a connection to ProtonVPN.

[Screenshot 06: VPN connection details](./screenshots/06-vpn-connection.png)

### Task 7: When was the first VPN connection observed? (Format: YYYY-MM-DD HH:MM:SS)

The timestamp for the first VPN connection was visible in the First Connect Local column of the registry key found in the previous task.

### Task 8: There were three shared folders observed on his machine. What is the path of the third share?

I found information about shared folders by searching the registry for "Shares". I found a key named Shares under the SYSTEM hive. The third shared folder listed had a path that seemed to contain a secret recipe.

[Screenshot 07: Shared folders from registry](./screenshots/07-shared-folders.png)

### Task 9: What is the last DHCP IP assigned to this host?

To find the host's DHCP information, I went back to the SYSTEM hive and followed the path SYSTEM\ControlSet001\Services\Tcpip\Parameters\Interfaces. By selecting the last interface in the list, I could find the last assigned DHCP IP address.

[Screenshot 08: Last DHCP IP assigned to host](./screenshots/08-dhcp-ip.png)

### Task 10: The suspect seems to have accessed a file containing the secret coffee recipe. What is the name of the file?

To find a recently accessed file, I examined the NTUSER.DAT hive. The path NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs contained a list of recently opened files. I saw a file with a name that fit the description.

[Screenshot 09: Secret recipe file name](./screenshots/09-secret-recipe-file.png)

### Task 11: The suspect executed multiple commands using the Run window. What command was used to enumerate the network interfaces?

I looked into the RunMRU (Most Recently Used) key within the NTUSER.DAT hive. This key logs commands executed from the Run window. The RunMRU key contained a command used to enumerate network interfaces.

[Screenshot 10: Executed network command](./screenshots/10-run-command.png)

### Task 12: The user searched for a network utility tool to transfer files using the file explorer. What is the name of that tool?

To find what the user searched for in Windows Explorer, I checked the NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery key. The logs showed the name of a network utility tool.

[Screenshot 11: Network utility search query](./screenshots/11-search-query.png)

### Task 13: What is the recent text file opened by the suspect?

Returning to the RecentDocs key, I could see a recently opened text file that was a key part of the "secret recipe" theme.

### Task 14: How many times was PowerShell executed on this host?

The UserAssist key tracks the execution count of various applications. I went to NTUSER.DAT\Software\Microsoft\Windows\Currentversion\Explorer\UserAssist\{GUID}\Count and found the entry for PowerShell, which showed it was executed 3 times.

[Screenshot 12: PowerShell execution count](./screenshots/12-powershell-count.png)

### Task 15: The suspect also executed a network monitoring tool. What is the name of the tool?

Within the same UserAssist key, I saw an entry for a network monitoring tool that the suspect had executed.

[Screenshot 13: Network monitoring tool (Wireshark)](./screenshots/13-network-monitoring-tool.png)

### Task 16: Registry Hives also note the amount of time a process is in focus. Examine the Hives and confirm for how many seconds was ProtonVPN executed?

The UserAssist key also tracks the "Focus Time" for applications. I found the entry for ProtonVPN and converted its focus time into total seconds.

[Screenshot 14: ProtonVPN focus time](./screenshots/14-vpn-focus-time.png)

### Task 17: Everything.exe is a utility used to search for files in a Windows machine. What is the full path from which everything.exe was executed?

I found the full path for everything.exe in the UserAssist key, on the same screen as the entry for the network monitoring tool.

---

## Summary
This room was a deep and practical exploration of Windows Registry forensics. I learned how to:

Navigate and interpret the different registry hives (SYSTEM, SAM, SOFTWARE, and NTUSER.DAT).

Extract crucial information about the system, users, network connections, and recent file access.

Identify attacker TTPs by uncovering executed commands and suspicious backdoor accounts.

Correlate different registry keys to build a comprehensive picture of user activity, from what a user searched for to how long an application was in focus.

Mastering registry analysis is an essential skill for any digital forensic investigator.