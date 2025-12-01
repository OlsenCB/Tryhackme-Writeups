# TryHackMe Room: Splunk: Setting up a SOC Lab

This room provided a practical, step-by-step guide to setting up a foundational Security Operations Center (SOC) lab environment using Splunk. The focus was on deploying Splunk on both Linux and Windows, configuring Universal Forwarders, and ingesting critical system and application logs to begin active monitoring.

---

## Section: Splunk: Deployment on Linux Server

### Task 1: What is the default port for Splunk?

The default port used for accessing the Splunk Web interface is 8000.

---

## Section: Splunk: Interacting with CLI

### Task 1: In Splunk, what is the command to search for the term coffely in the logs?

When interacting with the Splunk CLI tool, the command used to perform a search is: ./bin/splunk search coffely

---

## Section: Splunk: Data Ingestion

### Task 1: What is the default port, on which Splunk Forwarder runs on?

The Splunk Forwarder, used to send data from remote machines to the indexer, runs by default on port 8089.

---

## Section: Configuring Forwarder on Linux

### Task 1: Follow the same steps and ingest /var/log/auth.log file into Splunk index Linux_logs. What is the value in the sourcetype field?

After following the room's instructions to set up the Linux Universal Forwarder and ingest the /var/log/auth.log file into the Linux_logs index, I searched for the new events in the Splunk UI. I inspected the extracted fields to find the value assigned to the sourcetype.

[Screenshot 01: Sourcetype value for auth.log](./screenshots/01-sourcetype-auth.log.png)

### Task 2: Create a new user named analyst using the command adduser analyst. Once created, look at the events generated in Splunk related to the user creation activity. How many events are returned as a result of user creation?

I executed the adduser analyst command on the Linux forwarder host. I then searched Splunk for the new events in the Linux_logs index related to this activity. The user creation process generated multiple corresponding log events.

[Screenshot 02: Events generated after user creation](./screenshots/02-user-creation-events.png)

### Task 3: What is the path of the group the user is added after creation?

By examining the logs generated during the user creation process, I could find the specific file path that was modified to add the new user to a group. The path is /etc/group.

---

## Section: Splunk: Installing on Windows

### Task 1: What is the default port Splunk runs on?

As with the Linux version, the default port for the Splunk Web interface on Windows is 8000.

### Task 2: Click on the Add Data tab; how many methods are available for data ingestion?

In the Splunk UI, clicking the "Add Data" tab presented three main methods for data ingestion: upload, monitor, and forward.

[Screenshot 03: Data ingestion methods count](./screenshots/03-ingestion-methods.png)

### Task 3: Click on the Monitor option; what is the first option shown in the monitoring list?

Selecting the "Monitor" option shows a list of local data sources available for monitoring. The very first option listed is Local Event Logs.

[Screenshot 04: First option in monitoring list](./screenshots/04-first-monitor-option.png)

---

## Section: Installing and Configuring Forwarder

### Task 1: What is the full path in the C:\Program Files where Splunk forwarder is installed?

During the installation of the Splunk Universal Forwarder on the Windows machine, the default installation path in the C:\Program Files directory was noted. The full path is: C:\Program Files\SplunkUniversalForwarder

[Screenshot 05: Universal Forwarder installation path](./screenshots/05-forwarder-install-path.png)

### Task 2: What is the default port on which Splunk configures the forwarder?

When configuring the forwarder to communicate with the indexer, the default port used is 9997.

---

## Section: Splunk: Ingesting Windows Logs

### Task 1: While selecting Local Event Logs to monitor, how many Event Logs are available to select from the list to monitor?

When configuring the monitoring of Windows Event Logs, the UI provides a predefined list of logs available for ingestion. I counted the number of available logs in this list.

[Screenshot 06: Count of available Windows Event Logs](./screenshots/06-event-logs-count.png)

### Task 2: Search for the events with EventCode=4624. What is the value of the field Message?

I searched for events with EventCode=4624 (which typically indicates a successful logon) in the Windows logs. By expanding one of the resulting events, I read the content of the Message field to find its value.

[Screenshot 07: Message field for EventCode 4624](./screenshots/07-event-4624-message.png)

---

## Section: Ingesting Coffely Web Logs

### Task 1: In the lab, visit http://coffely.thm/secret-flag.html; it will display the history logs of the orders made so far. Find the flag in one of the logs.

I navigated to the specified URL in the lab environment. The page displayed historical order logs. By reviewing the content of the logs, I found the embedded flag.

[Screenshot 08: Flag found in web logs](./screenshots/08-web-logs-flag.png)

---

# Summary

This room was a highly effective exercise in establishing the groundwork for a robust security monitoring system. I gained practical experience in:

-Cross-Platform Deployment: Understanding the basics of deploying and interacting with Splunk on both Linux and Windows.
-Universal Forwarder Setup: Configuring the Splunk Universal Forwarder on Linux to send critical system logs (auth.log) to the central indexer.
-Windows Log Ingestion: Using the forwarder to monitor and ingest structured Windows Event Logs and understanding their associated event codes.
-Troubleshooting: Identifying log-generating activities (like user creation) and immediately verifying their presence and structure in the Splunk index.
-Log Hunting: Practicing basic log review by searching for specific indicators (like a flag) within application logs.