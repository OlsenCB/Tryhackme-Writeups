# TryHackMe – Investigating Windows

## Room Description
The **Investigating Windows** room is a hands-on challenge in analyzing a compromised Windows machine.  
The goal is to connect to the VM, investigate artifacts, identify persistence mechanisms, and trace the attacker’s actions.

---

## Steps I Took

### 1. Accessing the machine
I connected to the target via RDP after configuring OpenVPN.  

- IP: `10.10.12.82`  
- Username: `Administrator`  
- Password: `letmein123!`  

[Screenshot 1: RDP login](./screenshots/01-rdp-login.png)

---

### 2. Identifying the operating system
In PowerShell, I checked OS details:  

Get-ComputerInfo -Property "Os*"

[Screenshot 2: OS information](./screenshots/02-os-info.png)

### 3. Checking user accounts and logons

I listed local users:
Get-LocalUser
Users present: Administrator, Guest, Jenny, John.

I then checked logon history with individual queries for each account.

[Screenshot 3: Last logon users](./screenshots/03-last-logons.png)

Result: Administrator was the last logged-in user.
From this output I also noted John’s last logon time (used for later questions).

### 4. Detecting suspicious network connections

On startup, consoles flashed briefly with IP information.
To confirm, I inspected the hosts file:

C:\Windows\System32\drivers\etc\hosts

The entries suggested DNS poisoning.
To validate persistence, I checked registry autoruns:

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run

[Screenshot 4: Registry Run key with suspicious IP](./screenshots/04-registry-run-ip.png)

### 5. Checking administrator group membership

I listed members of the Administrators group:
Get-LocalGroupMember -Group "Administrators"
Result: besides Administrator, also Jenny and Guest had admin rights.

[Screenshot 5: Local Administrators group](./screenshots/05-admin-group.png)

### 6. Identifying malicious scheduled task

To view tasks:
Get-ScheduledTask
Filtered only root tasks:
Get-ScheduledTask | where ($_.TaskPath -eq "\")
Malicious task detected: Clean file system.

[Screenshot 6: Scheduled tasks list](./screenshots/06-scheduled-tasks.png)

### 7. Investigating scheduled task actions

I extracted its details:
$task = Get-ScheduledTask | where TaskName -EQ "Clean file system"
$task.Actions
This showed the file path executed daily.

[Screenshot 7: Malicious task actions](./screenshots/07-task-actions.png)
From the arguments I identified the local port the malware was listening on.

### 8. Determining compromise date

On drive C:\, I inspected folder modification timestamps.

[Screenshot 8: Folder modification dates](./screenshots/08-folder-dates.png)

### 9. Checking event logs for special logons

I opened Event Viewer → Security Log and created a custom view:

Time range: 3/2/2019 4:00PM – 3/2/2019 4:30PM.

Suspicious logon events with elevated privileges appeared in this window.

[Screenshot 9: Event Viewer suspicious logon](./screenshots/09-eventviewer.png)

### 10. Identifying credential dumping tool

Pop-up windows revealed a path: C:\TMP\mim.exe.
This strongly suggested Mimikatz.

To confirm, I checked mim-out which contained credential dump output.

[Screenshot 10: mimikatz evidence](./screenshots/10-mimikatz.png)

### 11. Attacker’s C2 IP

The hosts file showed tampered entries for google.com.
Pinging google.com returned 142.250.64.110, which did not match the poisoned IP.

[Screenshot 11: Hosts file contents](./screenshots/11-hosts.png)

### 12. Investigating web server artifacts

In C:\inetpub\wwwroot, I found three files, two of which had .jsp extensions.

.jsp (Java Server Pages) are server-side files executed by a Java-based web server.

Attackers likely uploaded them as backdoors to execute commands remotely.

[Screenshot 12: Inetpub folder with .jsp files](./screenshots/12-inetpub.png)

### 13. Finding last opened port

In Windows Firewall → Inbound Rules, I filtered for rules without a group.
Two suspicious rules appeared, one with an unusual open port (likely attacker-created).

[Screenshot 13: Firewall inbound rules](./screenshots/13-firewall-port.png)

### 14. DNS poisoning target

From the hosts file, it was clear that Google was the site attacked via DNS poisoning.

[Screenshot 14: Hosts file reference (see Screenshot 11)](./screenshots/11-hosts.png)

---

## Lessons Learned

Compromise left multiple artifacts: registry autoruns, scheduled tasks, poisoned hosts file.

Attackers escalated privileges by adding Guest and Jenny to the Administrators group.

Persistence mechanisms included scheduled tasks and DNS poisoning.

Common offensive tools such as Mimikatz were used to harvest credentials.

.jsp backdoors highlight the risk of poorly secured web servers.

---

## Summary

Challenge Type: Windows forensics & incident response

Tools used: PowerShell, Event Viewer, Registry Editor, RDP

Key Findings:

Last logged-in user: Administrator

Malicious scheduled task: Clean file system

Tool used: Mimikatz (mim.exe)

Attacker persistence via registry + scheduled tasks

C2 communication and DNS poisoning observed

Malicious web artifacts: .jsp files

Screenshots: 13