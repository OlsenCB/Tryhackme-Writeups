# TryHackMe Room: Velociraptor

This room was a deep dive into Velociraptor, a powerful open-source endpoint visibility and live forensics tool. The platform uses its own query language, VQL, to perform complex, targeted data collection and analysis across a network of endpoints. I explored its deployment, client interaction, and various forensic capabilities.

---

## Section: Deployment

### Task 1: Using the documentation, how would you launch an Instant Velociraptor on Windows?

According to the official documentation, the command to launch an instant version of Velociraptor for quick analysis on Windows is velociraptor.exe gui.

---

## Section: Interacting with Client Machines

### Task 1: What is the hostname for the client?

After logging in to the Velociraptor GUI, the hostname of the connected client was visible on the main dashboard.

[Screenshot 01: Hostname](./screenshots/01-hostname.png)

### Task 2: What is listed as the agent version?

I clicked on the client's ClientID to open the Overview page. The agent version was listed there.

[Screenshot 02: Agent Version](./screenshots/02-agent-version.png)

### Task 3: In the Collected tab, what was the VQL command to query the client user accounts?

I navigated to the Collected tab and then to the Requests section. By scrolling down, I found a Query field that showed the VQL command used to query user accounts.

[Screenshot 03: VQL command to query user accounts](./screenshots/03-vql-query.png)

### Task 4: In the Collected tab, check the results for the PowerShell whoami command you executed previously. What is the column header that shows the output of the command?

I found the log for the whoami command I had previously executed in the shell. The output was displayed under the Stdout column.

[Screenshot 04: Stdout column output](./screenshots/04-stdout-column.png)

### Task 5: In the Shell, run the following PowerShell command Get-Date. What was the PowerShell command executed with VQL to retrieve the result?

I went to the Shell tab and executed the PowerShell command get-date (it's important to use lowercase letters). I then checked the log for this command and found the VQL command that was used to execute it.

[Screenshot 05: VQL command for Get-Date](./screenshots/05-vql-get-date.png)

---

## Section: Creating a New Collection

### Task 1: Earlier you created a new artifact collection for Windows.KapeFiles.Targets. You configured the parameters to include Ubuntu artifacts. Review the parameter description for this setting. What is this parameter specifically looking for?

To find the answer, I went to View Artifacts, searched for Windows.KapeFiles.Targets, and reviewed the description for the parameter related to Ubuntu artifacts. The description indicated that the parameter was looking for a list of paths.

[Screenshot 06: Ubuntu artifacts parameter description](./screenshots/06-ubuntu-description.png)

### Task 2: Review the output. How many files were uploaded?

After the artifact collection was completed, I reviewed the results. The total number of files uploaded was clearly visible.

[Screenshot 07: Number of files uploaded](./screenshots/07-files-uploaded.png)

---

## Section: VFS (Virtual File System)

### Task 1: Which accessor can access hidden NTFS files and Alternate Data Streams? (format: xyz accessor)

The room's documentation on VFS accessors provided a clear answer. The ntfs accessor is used to access hidden files and Alternate Data Streams (ADS).

[Screenshot 08: VFS accessors documentation](./screenshots/08-vfs-accessors.png)

### Task 2: Which accessor provides file-like access to the registry? (format: xyz accessor)

The answer to this question was found in the last sentence on the same documentation page, which indicated that the registry accessor provides file-like access to the registry.

### Task 3: What is the name of the file in $Recycle.Bin?

I navigated the Virtual File System in the Velociraptor GUI. By clicking through the folders, I eventually located the $Recycle.Bin directory. Inside, I found the file named desktop.ini.

[Screenshot 09: File in Recycle Bin](./screenshots/09-recycle-bin.png)

### Task 4: There is hidden text in a file located in the Admin's Documents folder. What is the flag?

I browsed to the Administrator's Documents folder within the VFS and found a file named flag.txt. I clicked on the Collect from the Client option to view its contents, which revealed the flag.

[Screenshot 10: Hidden flag in text file](./screenshots/10-hidden-flag.png)

---

## Section: VQL (Velociraptor Query Language)

### Task 1: What is followed after the SELECT keyword in a standard VQL query?

I referred to the documentation on VQL syntax to answer this and the following two questions. The documentation clearly showed that Column Selectors follows the SELECT keyword.

[Screenshot 11: VQL syntax documentation](./screenshots/11-vql-syntax.png)


### Task 2: What goes after the FROM keyword?

According to the syntax shown in the documentation, VQL plugin follows the FROM keyword.

### Task 3: What is followed by the WHERE keyword?

The documentation also showed that an Filter Condition follows the WHERE keyword.

### Task 4: What can you type in the Notepad interface to view a list of possible completions for a keyword?

The documentation's section on Plugins showed that typing ? in the notebook editor brings up a list of possible completions for a keyword.

[Screenshot 12: VQL plugins completion](./screenshots/12-vql-plugins.png)

Task 5: What plugin would you use to run PowerShell code from Velociraptor?

By checking the documentation on Extending VQL, I found a section on Extending artifacts - PowerShell, which indicated that the correct answer is execve().

[Screenshot 13: PowerShell plugin documentation](./screenshots/13-powershell-plugin.png)

---

## Section: Forensic Analysis VQL Plugins

### Task 1: What plugin would be used for parsing the Master File Table (MFT)?

The documentation for this section had a part on Parsing the MFT, which directly stated that the parse_mft plugin is used for this purpose.

[Screenshot 14: MFT plugin documentation](./screenshots/14-mft-plugin.png)

### Task 2: What filter expression will ensure that no directories are returned in the results?

The documentation showed that the IsDir filter expression can be used to exclude directories from the results.

[Screenshot 15: Filter expression for directories](./screenshots/15-is-dir-filter.png)

---

## Section: Hunt for a nightmare

### Task 1: What is the name in the Artifact Exchange to detect Printnightmare?

I opened the documentation for the Artifact Exchange and searched for "Printnightmare". The entry for a detection artifact was named Windows.EventLogs.PrintNightmareDetection.

[Screenshot 16: PrintNightmare detection artifact](./screenshots/16-printnightmare-artifact.png)

### Task 2: Per the above instructions, what is your Select clause? (no spaces after commas)

Based on the instructions and the provided components, I constructed the full SELECT clause for the VQL query.


SELECT "C:/" + FullPath AS full_path,FileName AS file_name,parse_pe(file="C:/" + FullPath) AS PE

### Task 3: What is the name of the DLL that was placed by the attacker?

I combined the SELECT clause from the previous task with a FROM and WHERE clause from a file on the desktop of the new VM. I then created a new notebook and ran the full query. After the query completed, I was able to see the name of the DLL in the file_name column of the results. The name was the same as the detection artifact from Task 1.

[Screenshot 17: Attacker's DLL and PDB entry](./screenshots/17-attacker-dll-pdb.png)

### Task 4: What is the PDB entry?

The PDB entry was visible in the output on the screen, within the PDB column of the results.

---

## Summary

This room was a very detailed and practical walkthrough of Velociraptor as a digital forensics and incident response tool. I learned how to:

Deploy and interact with Velociraptor clients.

Use the Virtual File System to browse and collect files from endpoints.

Understand and write Velociraptor Query Language (VQL).

Apply powerful forensic VQL plugins for tasks like parsing the MFT and analyzing PE files.

Use my new skills to perform a simulated threat hunt for a known vulnerability (PrintNightmare).

Velociraptor proved to be an incredibly flexible and efficient tool for live forensics, offering deep visibility into endpoints.