# TryHackMe Room: Tardigrade

This room involves investigating a compromised Linux server to uncover persistence mechanisms and malicious modifications. I acted as an incident responder, exploring user directories, analyzing shell configurations, and identifying compromised system accounts.

---

## Section: Connect to the machine via SSH

### Task 1: What is the server's OS version?

After connecting via SSH, I checked the standard release file to identify the operating system version.

cat /etc/os-release

Looking at the VERSION or PRETTY_NAME line gave me the answer.

[Screenshot 01: OS Version from /etc/os-release](./screenshots/01-os-version.png)

---

## Section: Investigating the giorgio account

### Task 1: What's the most interesting file you found in giorgio's home directory?

I started by listing all files in the current directory, including hidden ones, using ls -la.

ls -la

Among the standard configuration files, one hidden file stood out immediately due to its suspicious name: .bad_bash.

[Screenshot 02: Hidden .bad_bash file listing](./screenshots/02-bad-bash-file.png)

### Task 2: Another file that can be found in every user's home directory is the .bashrc file. Can you check if you can find something interesting in giorgio's .bashrc?

The .bashrc file is a common place for attackers to hide persistence mechanisms because it executes every time a user opens a new shell. I inspected its contents:

cat .bashrc

I found a malicious alias for the ls command. Instead of just listing files, it was configured to execute a malicious command (likely a reverse shell or beacon) in the background.

[Screenshot 03: Malicious alias in .bashrc](./screenshots/03-bashrc-alias.png)

### Task 3: Did you find anything interesting about scheduled tasks?

After checking the file system, I moved on to scheduled tasks, another common persistence vector. I checked the current user's crontab.

crontab -e

I found a suspicious entry scheduled to run automatically.

[Screenshot 04: Suspicious crontab entry](./screenshots/04-crontab-entry.png)

---

## Section: Dirty Wordlist Revisited

### Task 1: What is the flag?

This section emphasized the importance of keeping a "dirty wordlist" of findings during an investigation. I accepted the advice and grabbed the bonus flag.

Answer: THM{d1rty_w0rdl1st}

---

## Section: Investigating the root account

### Task 1: A few moments after logging on to the root account, you find an error message in your terminal. What does it say?

I switched to the root account. Because of the malicious alias discovered earlier (which was likely present for root as well), simply typing ls (or just logging in if the profile loaded) triggered the malicious command.

The terminal displayed a connection error: ncat: Connection timed out.

[Screenshot 05: ncat connection timeout error](./screenshots/05-ncat-error.png)

### Task 2: What command was displayed?

As seen in the error message above, the command attempting to execute was ncat (trying to connect back to an attacker's IP).

### Task 3: Can you find out how the suspicious command has been implemented?

This behavior confirmed that the malicious alias I found in .bashrc earlier was active. The attacker modified the shell configuration so that common commands would trigger their reverse shell payload.

---

## Section: Investigating the system

### Task 1: What is the last persistence mechanism?

To find system-wide persistence, I checked the /etc/passwd file to look for anomalies in user accounts.

cat /etc/passwd

I scanned the list for standard accounts that might have been modified. The account nobody is a standard Linux account, but it is often abused for persistence if an attacker can modify its home directory or shell.

[Screenshot 06: Checking /etc/passwd for the nobody account](./screenshots/06-passwd-nobody.png)

---

## Section: Final Thoughts

### Task 1: The adversary left a golden nugget of "advise" somewhere. What is the nugget?

To investigate the nobody account further, I checked its home directory defined in /etc/passwd. In this specific compromised machine, the home directory was set to /nonexistent (or I navigated to the root directory depending on the config).

Inside that directory, I found a hidden file named .youfoundme. I read it to retrieve the final flag/advice.

cat .youfoundme

[Screenshot 07: Final flag in .youfoundme](./screenshots/07-final-flag.png)

---

# Summary

In this room, I practiced Linux live forensics by:

- Enumerating Hidden Files: Using ls -la to find suspiciously named artifacts (.bad_bash).
- Analyzing Shell Configuration: identifying malicious aliases in .bashrc that hijacked standard commands like ls.
- Checking Scheduled Tasks: Reviewing crontab for persistence.
- Investigating System Accounts: analyzing /etc/passwd to identify compromised service accounts like nobody.