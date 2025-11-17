# TryHackMe Room: Intro to Logs

This room was a foundational course covering the essential concepts, practices, and tools involved in log management and analysis within a security context. I explored log types, formats, collection mechanisms (rsyslog), retention policies (logrotate), and the crucial steps of normalization and enrichment. This writeup details the steps taken to investigate a simulated corporate environment.

---

## Section: Expanding Perspectives: Logs as Evidence of Historical Activity

### Task 1: What is the name of your colleague who left a note on your Desktop?

After accessing the target machine, I found a text file on the desktop containing a note with crucial information for the investigation. The name of the colleague who left the note was found on the 17th line of the file.

[Screenshot 01: Colleague's note on Desktop](./screenshots/01-colleague-note.png)

### Task 2: What is the full path to the suggested log file for initial investigation?

The suggested path for the initial log file was also found within the text file, specifically on the seventh line.

---

## Section: Types, Formats, and Standards

### Task 1: Based on the list of log types in this task, what log type is used by the log file specified in the note from Section 2?

The log file mentioned in the note, access.log, contained information about HTTP requests and suspicious web browser activity. By examining its content and purpose, I determined it is categorized as a Web server log.

[Screenshot 02: Access log contents and format](./screenshots/02-access-log-content.png)

### Task 2: Based on the list of log formats in this task, what log format is used by the log file specified in the note from section 2?

By comparing the structure of the entries in the access.log file with the provided examples in the task, I identified that the log format used is the Combined Log Format.

---

## Section: Collection, Management, and Centralisation

### Task 1: After configuring rsyslog for sshd, what username repeatedly appears in the sshd logs at /var/log/websrv-02/rsyslog_sshd.log, indicating failed login attempts or brute forcing?

To complete this task, I first had to configure rsyslog to capture SSH daemon logs:

1. Verified rsyslog status: sudo systemctl status rsyslog.
2. Created the configuration file: sudo nano /etc/rsyslog.d/98-websrv-02-sshd.conf.
3. Added the configuration to forward SSH messages to a new log file:

$FileCreateMode 0644
:programname, isequal, "sshd" /var/log/websrv-02/rsyslog_sshd.log

4. Restarted the service: sudo systemctl restart rsyslog.

After configuration, I checked the new log file's contents and quickly identified a repeatedly failing username, suggesting a brute-force attempt.

[Screenshot 03: Failed login attempts in sshd log](./screenshots/03-rsyslog-sshd.png)

### Task 2: What is the IP address of SIEM-02 based on the rsyslog configuration file /etc/rsyslog.d/99-websrv-02-cron.conf, which is used to monitor cron messages?

I examined the pre-existing rsyslog configuration file used to monitor cron messages. The file contained the IP address where the logs were being forwarded (the SIEM server).

[Screenshot 04: SIEM IP from rsyslog config](./screenshots/04-rsyslog-config-ip.png)

### Task 3: Based on the generated logs in /var/log/websrv-02/rsyslog_cron.log, what command is being executed by the root user?

I checked the contents of the rsyslog_cron.log file and observed a command being executed repeatedly by the root user, indicating a scheduled task.

[Screenshot 05: Root command from cron log](./screenshots/05-cron-command.png)

---

## Section: Storage, Retention, and Deletion

### Task 1: Based on the logrotate configuration /etc/logrotate.d/99-websrv-02_cron.conf, how many versions of old compressed log file copies will be kept?

I used cat to examine the logrotate configuration file for the cron logs to determine the retention policy.

cat /etc/logrotate.d/99-websrv-02_cron.conf

[Screenshot 06: Logrotate configuration details](./screenshots/06-logrotate-config.png)

The rotate directive showed the number of old compressed log file copies that will be kept.

### Task 2: Based on the logrotate configuration /etc/logrotate.d/99-websrv-02_cron.conf, what is the log rotation frequency?

The rotation frequency was clearly indicated by the relevant directive in the configuration file.

---

## Section: Hands-on Exercise: Log analysis process, tools, and techniques

### Task 1: Upon accessing the log viewer URL for unparsed raw log files, what error does "/var/log/websrv-02/rsyslog_cron.log" show when selecting the different filters?

I navigated to the provided log viewer URL in the browser and clicked on the filters option. When viewing the logs for rsyslog_cron.log, an error message appeared, highlighting a common issue with unstandardized log files.

[Screenshot 07: Log viewer error message](./screenshots/07-log-viewer-error.png)

### Task 2: What is the process of standardising parsed data into a more easily readable and query-able format?

The process of structuring and standardizing parsed data is referred to as Normalisation.

### Task 3: What is the process of consolidating normalised logs to enhance the analysis of activities related to a specific IP address?

The process of consolidating normalized logs with external data (like geolocation or threat feeds) to enhance analysis is known as Enrichment.

---

# Summary

This room provided a solid foundation in log management and forensics, covering the entire lifecycle of a log entry. I learned how to:

-Identify different log types and formats (e.g., Web server logs, Combined Format).
-Configure and manage log collection using a system utility like rsyslog on a Linux server.
-Define log retention policies using logrotate directives (rotate, hourly).
-Understand the crucial processes of Normalisation and Enrichment, which are necessary steps before logs can be effectively searched and analyzed in a SIEM platform.
-Diagnose common log parsing issues, such as missing timestamps (No date field).