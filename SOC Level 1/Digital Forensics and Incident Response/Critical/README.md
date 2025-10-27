# TryHackMe Room: Critical

This room provided an excellent hands-on introduction to memory forensics using the powerful Volatility framework. The scenario involved a compromised machine, and my task was to analyze a memory dump to uncover suspicious activity, malicious processes, and exfiltrated data. I learned how to use a variety of Volatility plugins to investigate a host's memory from a static file.

---

## Section: Memory Forensics

### Task 1: What type of memory is analyzed during a forensic memory task?

Based on the room's introduction, the type of memory that is analyzed during a forensic memory task is RAM (Random Access Memory).

### Task 2: In which phase will you create a memory dump of the target system?

According to the provided information, the process of creating a memory dump is part of the memory acquisition phase. This is done to preserve a copy of the live memory for analysis.

---

## Section: Environment & Setup

### Task 1: Which plugin can help us to get information about the OS running on the target machine?

The plugin used to gather information about the operating system is windows.info.

### Task 2: Which tool referenced above can help us take a memory dump on a Linux OS?

The tool mentioned in the room that can be used to take a memory dump on a Linux OS is LIME.

### Task 3: Which command will display the help menu using Volatility on the target machine?

The command to display the help menu for Volatility is vol -h.

---

## Section: Gathering Target Information

### Task 1: Is the architecture of the machine x64 (64bit) Y/N?

To start the analysis of the memory dump, I used the windows.info plugin to get an overview of the target system.

vol -f memdump.mem windows.info

[Screenshot 01: Windows OS info](./screenshots/01-windows-info.png)

The output showed that the machine's architecture is x64.

### Task 2: What is the Verison of the Windows OS

The OS version was listed in the windows.info output under PE MajorOperatingSystemVersion.

### Task 3: What is the base address of the kernel?

The base address of the kernel was also found on the same screen, in the Kernel Base field.

---

## Section: Searching for Suspicious Activity

### Task 1: Using the plugin "windows.netscan". Can you identify the destination IP address where a connection is established on port 80?

To find any established network connections, I used the windows.netscan plugin. I piped the output to grep to filter for anything on port 80.

vol -f memdump.mem windows.netscan | grep 80

[Screenshot 02: Network connections on port 80](./screenshots/02-netscan-port-80.png)

The results showed a connection to 192.168.182.128 that was in an ESTABLISHED state.

### Task 2: Using the plugin "windows.netscan," can you identify the program (owner) used to access through port 80?

The program responsible for the connection to port 80 was identified in the output from the previous task as msedge.exe.

### Task 3: Analyzing the processes present on the dump, what is the PID of the child process of critical_updat?

To find the child process, I used the windows.pstree plugin to view the process tree. I filtered for the parent process, critical_updat, and found its PID. I then looked for a process with that PID as its PPID.

vol -f memdump.mem windows.pstree | grep critical_updat

[Screenshot 03: Child process PID from pstree](./screenshots/03-pstree-child-process.png)

### Task 4: What is the time stamp time for the process with the truncated name critical_updat?

The timestamp for the critical_updat process was visible in the second-to-last column of the windows.pstree output.

---

## Section: Finding Interesting Data

### Task 1: Analyzing the "windows.filescan" output, what is the full path and name for critical_updat?

To find the full file path of the malicious program, I used the windows.filescan plugin and filtered for critical_updat.

vol -f memdump.mem windows.filescan | grep critical_updat

[Screenshot 04: Full path from filescan](./screenshots/04-filescan-path.png)


### Task 2: Analyzing the "windows.mftscan.MFTScan" what is the Timestamp for the created date of important_document.pdf?

I used the windows.mftscan.MFTScan plugin to get information about the file system, including the timestamp for a specific file.

vol -f memdump.mem windows.mftscan.MFTScan | grep important_document.pdf

[Screenshot 05: PDF creation timestamp](./screenshots/05-pdf-timestamp.png)

### Task 3: Analyzing the updater.exe memory output, can you observe the HTTP request and determine the server used by the attacker?

To find the server used by the attacker, I first had to dump the memory of the suspicious updater.exe process (PID 1612).

vol -f memdump.mem -o . windows.memmap --dump --pid 1612

Next, I used the strings command on the newly created dump file and searched for the keyword http. This revealed a request for an encKey.txt file.

strings pid.1612.dmp | grep http

[Screenshot 06: HTTP request from memory dump](./screenshots/06-http-request.png)

Finally, to get more context around the request and find the full server name, I used strings again with a wider search range.

strings pid.1612.dmp | grep -B 10 -A 10 "http://key.critical-update.com/encKEY.txt"

[Screenshot 07: Attacker's server from memory dump](./screenshots/07-attacker-server.png)

The output showed the full server name used by the attacker.

---

## Summary
This room was a highly effective hands-on lesson in memory forensics. I learned how to:

Use various Volatility plugins to get a comprehensive overview of a system's state from a single memory dump.

Identify running processes (windows.pstree) and their network connections (windows.netscan).

Find files and their metadata from the master file table (windows.mftscan) and file handles (windows.filescan).

Go deeper by dumping the memory of a single process and using external tools like strings to analyze its contents for valuable information like C2 servers and URLs.

This process is a fundamental skill for any digital forensic investigator and incident responder, as it allows for a powerful way to analyze a compromised system's state at a single point in time.