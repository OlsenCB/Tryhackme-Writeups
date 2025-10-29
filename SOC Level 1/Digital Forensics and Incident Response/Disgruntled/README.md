\# TryHackMe Room: Disgruntled



This room presented a scenario where a disgruntled employee had compromised a system. My task was to act as a digital forensic investigator and follow a trail of evidence left behind to uncover the malicious activity. The investigation relied heavily on analyzing system logs and hidden configuration files to piece together the entire timeline of events.



---



\## Section: Nothing suspicious... So far



\### Task 1: The user installed a package on the machine using elevated privileges. According to the logs, what is the full COMMAND?



My first step was to analyze the system's authentication logs, auth.log, to look for any suspicious activity. I used grep to search for the keyword "install." The initial auth.log file didn't contain the information, but I found the entry I needed in auth.log.1.



cat var/log/auth.log.1 | grep install



\[Screenshot 01: Install command and PWD](./screenshots/01-install-command.png)





\### Task 2: What was the present working directory (PWD) when the previous command was run?



The PWD was clearly visible in the log entry from the previous task.



---



\## Section: Let's see if you did anything bad



\### Task 1: Which user was created after the package from the previous task was installed?



I continued my investigation of the auth.log.1 file, this time searching for the adduser command, which is used to create new user accounts.



cat var/log/auth.log.1 | grep adduser



\[Screenshot 02: Created user log entry](./screenshots/02-created-user.png)



\### Task 2: A user was then later given sudo priveleges. When was the sudoers file updated? (Format: Month Day HH:MM:SS)



To find when the sudoers file was updated, I searched the same log file for the command visudo, which is the correct way to safely edit the file. The timestamp of the second entry was the answer.



cat var/log/auth.log.1 | grep visudo



\[Screenshot 03: Sudoers file update date](./screenshots/03-sudoers-date.png)



\### Task 3: A script file was opened using the "vi" text editor. What is the name of this file?



By examining the log output around the specific timestamp from the previous task, I found an entry that indicated a file named s bomb.sh was opened with the vi editor.



\[Screenshot 04: The name of the script](./screenshots/04-script-name.png)



---



\## Section: Bomb has been planted. But when and where?



\### Task 1: What is the command used that created the file bomb.sh?



Since the previous tasks showed that a new user named it-admin was created, I knew I should check that user's home directory for clues. The user's command history is stored in a hidden file called .bash\_history.



cat /home/it-admin/.bash\_history



\[Screenshot 05: Bash history of it-admin user](./screenshots/05-bash-history.png)





\### Task 2: The file was renamed and moved to a different directory. What is the full path of this file now?



The vim editor keeps a history of its sessions in a hidden file named .viminfo. By examining its contents, I could see that the file had been renamed and moved.



cat /home/it-admin/.viminfo



\[Screenshot 06: Viminfo file contents](./screenshots/06-viminfo-contents.png)



\### Task 3: When was the file from the previous question last modified? (Format: Month Day HH:MM)



Knowing the full path of the file, /bin/os-update.sh, I used the stat command to view its metadata, which included the last modification time.



stat /bin/os-update.sh



\[Screenshot 07: File modification time](./screenshots/07-stat-file-time.png)



\### Task 4: What is the name of the file that will get created when the file from the first question executes?



To find out what the malicious script would do, I needed to view its contents. I used the nano editor to safely open the file and read the code.



nano /bin/os-update.sh



\[Screenshot 08: Script file content](./screenshots/08-script-content.png)



The script revealed that it would create a file named goodbye.txt.



---



\## Section: Following the fuse



\### Task 1: At what time will the malicious file trigger? (Format: HH:MM AM/PM)



The last piece of the puzzle was to find when the malicious script was scheduled to run. I checked the system's cron schedule using the crontab command.



cat /etc/crontab



\[Screenshot 09: Malicious file trigger time](./screenshots/09-crontab-time.png)



The crontab entry showed that the malicious script was set to execute at 8:00 AM.



---



\## Summary

This room was an excellent exercise in Linux forensics. I learned how to:



Use command-line tools like cat, grep, and stat to analyze system logs.



Uncover and examine hidden user files, such as .bash\_history and .viminfo, to see what commands were run.



Follow a timeline of events from user creation and privilege escalation to the deployment of a malicious script.



Find the final trigger mechanism by checking the crontab file.



This process of chaining together different pieces of evidence from various system artifacts is fundamental to digital forensics and incident response.

