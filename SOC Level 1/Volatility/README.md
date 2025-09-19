# TryHackMe Room: Volatility

This room was an excellent introduction to Volatility, an open-source memory forensics framework. Volatility allows for the analysis of memory dumps (or "snapshots") from compromised systems to uncover running processes, network connections, loaded modules, and other key artifacts that are not visible through traditional file-based forensics. The room provided two distinct cases to investigate, which I did on the built-in TryHackMe machine.

---

## Case 001

### Task 1: What is the build version of the host machine in Case 001?

Volatility has a useful plugin called windows.info that provides general information about the memory dump, including the operating system, build number, and acquisition time.

vol -f /Scenarios/Investigations/Investigation-1.vmem windows.info

[Screenshot 01: Build version](./screenshots/01-build-version.png)

### Task 2: At what time was the memory file acquired in Case 001?

The acquisition time was visible in the SystemTime field from the output of the previous command.

### Task 3: What process can be considered suspicious in Case 001?

To find suspicious processes, I used the windows.pstree plugin, which shows the parent-child relationships of all processes, providing a clear view of the execution hierarchy.

vol -f /Scenarios/Investigations/Investigation-1.vmem windows.pstree

[Screenshot 02: Suspicious process](./screenshots/02-suspicious-process.png)

Looking at the output, the only non-essential process was the suspicious one.

### Task 4: What is the parent process of the suspicious process in Case 001?

 Volatility's pstree output clearly lists both the PID (Process ID) and PPID (Parent Process ID). By cross-referencing the PPID of the suspicious process with the PIDs in the list, I could identify its parent process.

### Task 5: What is the PID of the suspicious process in Case 001?

The PID was found directly next to the suspicious process name in the output from Task 3.

### Task 6: What is the parent process PID in Case 001?

As explained in Task 4, the PPID was also found directly in the output from the windows.pstree command.

### Task 7: What user-agent was employed by the adversary in Case 001?

This required a two-step process. First, I needed to dump the memory of the suspicious process. I used the windows.memmap.Memmap plugin with the --dump and --pid parameters to create a copy of the process's memory.

vol -f /Scenarios/Investigations/Investigation-1.vmem -o /tmp windows.memmap.Memmap --pid 1640 --dump

-f: specifies the input memory file.

-o /tmp: specifies the output directory for the dumped file.

windows.memmap.Memmap: the Volatility plugin used to map and inspect a process's memory.

--pid 1640: specifies the PID of the target process.

--dump: tells the plugin to dump the memory to a file.

Next, I used the strings command to extract all readable strings from the dumped file and piped the output to grep to search for "user-agent".

strings /tmp/pid.1640.dmp | grep -i "user-agent"

[Screenshot 03: User-agent strings](./screenshots/03-user-agent-strings.png)

### Task 8: Was Chase Bank one of the suspicious bank domains found in Case 001? (Y/N)

I used the same command from the previous task, but this time I searched for the word "chase" to see if there were any mentions of the bank.

strings /tmp/pid.1640.dmp | grep -i "chase"

The output showed numerous mentions, indicating that the answer was Y.

[Screenshot 04: Mentions of Chase](./screenshots/04-chase-mentions.png)

---

## Case 002

### Task 9: What suspicious process is running at PID 740 in Case 002?

I repeated the process from Task 3, but this time on the second memory file to find the suspicious processes.

vol -f /Scenarios/Investigations/Investigation-2.raw windows.pstree

[Screenshot 05: Processes from Investigation-2](./screenshots/05-case2-processes.png)

The process running at PID 740 was named @WanaDecryptor@. This is a significant indicator of a ransomware attack, as the name directly points to a decryption program.

### Task 10: What is the full path of the suspicious binary in PID 740 in Case 002?

I used the same two-step method as in Task 7 to dump the process's memory and search for strings.

vol -f /Scenarios/Investigations/Investigation-2.raw -o /tmp windows.memmap.Memmap --pid 740 --dump
strings /tmp/pid.740.dmp | grep -i wana

The output revealed the full path to the malicious binary.

[Screenshot 06: Full path of binary](./screenshots/06-full-path-binary.png)

### Task 11: What is the parent process of PID 740 in Case 002?

Looking at the output from the windows.pstree command in Task 9, I found that the parent process of PID 740 had a PID of 1940, which corresponded to tasksche.exe.

### Task 12: What is the suspicious parent process PID connected to the decryptor in Case 002?

As identified in the previous task, the parent process PID was 1940.

### Task 13: From our current information, what malware is present on the system in Case 002?

The name @WanaDecryptor@ is a clear indicator that the malware on the system is WannaCry, a well-known ransomware.

### Task 14: What DLL is loaded by the decryptor used for socket creation in Case 002?

A quick search confirmed that the decryptor used the ws2_32.dll to create sockets for its network communication.

[Screenshot 07: WannaCry DLL](./screenshots/07-wannacry-dll.png)

### Task 15: What mutex can be found that is a known indicator of the malware in question in Case 002?

I used the windows.handles plugin to find the mutex, which is a key indicator of malware presence.

vol -f /Scenarios/Investigations/Investigation-2.raw windows.handles

[Screenshot 08: WannaCry mutex](./screenshots/08-wannacry-mutex.png)

### Task 16: What plugin could be used to identify all files loaded from the malware working directory in Case 002?

I used the -h flag to view Volatility's help menu and piped the output to grep to find a plugin that could list files. The windows.filescan plugin was the correct one.

vol -h | grep windows.

[Screenshot 09: Volatility plugins help](./screenshots/09-volatility-plugins.png)

---

## Summary

This room was an excellent practical exercise in using Volatility for memory forensics. I learned how to:

Use core Volatility plugins like windows.info, windows.pstree, and windows.handles.

Dump a process's memory (windows.memmap.Memmap) and search it for key indicators.

Identify malicious processes by their names and parent-child relationships.

Correlate findings from the memory dump with external knowledge about malware like WannaCry to identify associated DLLs and mutexes.

Understand the purpose of various Volatility plugins for different types of analysis.