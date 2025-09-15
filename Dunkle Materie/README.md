# TryHackMe Room: Dunkle Materie

For this room we work directly on a virtual machine provided by TryHackMe, since it already contains the necessary tools and artifacts. The main tools used here are **ProcDot**, **Procmon logs**, and **Wireshark**.

---

## 1. Suspicious processes
I started ProcDot and loaded the log file:

C:\Users\Administrator\Desktop\Analysis File\Logfile.CSV


Filtering the results showed two suspicious processes both named **exploreer.exe**.  
Their **PIDs** are required as the first answer.  

[Screenshot 01: Suspicious processes and PIDs](./screenshots/01-suspicious-processes.png)

---

## 2. Full path of executed ransomware
To find the full path of the ransomware:

- I clicked the **System** process → **Refresh**
- Zoomed into the visualization and followed the suspicious processes
- Right click → **Details**

This revealed the full path of the ransomware binary.  

[Screenshot 02: Full path of the ransomware](./screenshots/02-ransomware-path.png)

---

## 3. C2 server domains
Next task: identify the two C2 server domains.

- First, I noted the suspicious IPs connected to the malicious process in ProcDot.  
  [Screenshot 03: C2 IPs](./screenshots/03-c2-ips.png)

- Using **Wireshark**, I applied the filter:
http.request.method == POST

and enabled **Resolve network (IP) addresses**. This revealed the first C2 domain.  

[Screenshot 04: First C2 domain](./screenshots/04-first-domain.png)

- For the second domain, I looked up the remaining IP in **VirusTotal**. In the **Community** section, someone had already mapped it to a domain.  

[Screenshot 05: Second C2 domain from VirusTotal](./screenshots/05-second-domain.png)

---

## 4. User-Agent and cloud security block
Following the TCP stream for the first domain (right click → *Follow* → *TCP stream*) revealed:

- The **User-Agent** string used by the C2 communication.  
- The **cloud security domain** that blocked the connection (visible in the `Server` response).

[Screenshot 06: User-Agent and security block](./screenshots/06-user-agent.png)

---

## 5. Ransomware wallpaper
Back in ProcDot, I examined the **exploreer.exe** process with PID `7128`.  
There I found the bitmap that was set as the new wallpaper during the ransomware attack.  

[Screenshot 07: Malicious wallpaper file](./screenshots/07-wallpaper.png)

---

## 6. PID responsible for wallpaper change
Tracing the arrows in ProcDot backwards from the wallpaper event led to the process PID that modified the desktop background.

[Screenshot 08: PID responsible for wallpaper change](./screenshots/08-pid-changing-wallpaper.png)

---

## 7. Mounted drive and registry path
Another task was to identify what drive was mounted and the corresponding registry key.  
By following the same PID I reached:
HKLM\SYSTEM\MountedDevices

[Screenshot 09: Malicious wallpaper bitmap file](./screenshots/09-wallpaper-file.png)


---

## 8. Name of the ransomware family
Finally, I returned to **VirusTotal**, this time searching by the previously discovered C2 domain.  
The **Community** tab revealed that the ransomware in this case was identified as **BlackMatter Ransomware**.  

[Screenshot 10: VirusTotal ransomware identification](./screenshots/10-ransomware.png)

---

## Summary
- Identified malicious processes (`exploreer.exe`) and their PIDs  
- Found the full path to the executed ransomware  
- Extracted C2 IPs and resolved them to domains (via Wireshark & VirusTotal)  
- Discovered User-Agent and cloud block domain  
- Recovered wallpaper bitmap and the PID responsible for changing it  
- Extracted mounted drive info from `HKLM\SYSTEM\MountedDevices`  
- Confirmed ransomware family: **BlackMatter**  

This exercise showed how ProcDot, Wireshark, and VirusTotal can be combined to investigate ransomware activity and trace attacker infrastructure.
