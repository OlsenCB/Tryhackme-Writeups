# TryHackMe Room: Fixit

This room was a practical challenge that required applying core Splunk data manipulation skills, specifically focused on fixing improperly indexed multi-line logs. The goal was to correctly define event boundaries, write a complex regular expression to extract custom fields from the raw log data, and then use the extracted fields to answer specific forensic questions.

---

## Section: FIXIT Challenge

### Task 1: What is the full path of the FIXIT app directory?

The first step was locating the application directory on the Splunk machine to modify its configuration files. The full path is: /opt/Splunk/etc/apps/fixit

### Task 2: What Stanza will we use to define Event Boundary in this multi-line Event case?

Since the logs were multi-line and we needed to define where the new event should start, the stanza used to define the event boundary before a specified pattern is BREAK_ONLY_BEFORE.

### Task 3: In the inputs.conf, what is the full path of the network-logs script?

I navigated to the fixit app directory and examined the inputs.conf file inside the default folder. This file specified the full path of the log script being monitored.

[Screenshot 01: Full path of network-logs script](./screenshots/01-inputs-conf.png)

### Task 4: What regex pattern will help us define the Event's start?

The log file provided in the challenge showed that every new log entry began with [Network-log]. I used this constant marker to define the event start in props.conf using the BREAK_ONLY_BEFORE stanza.

The necessary pattern is: \[Network-log\]

To fully fix the parsing, the props.conf file was configured as follows:

[network_logs]
SHOULD_LINEMERGE = true
BREAK_ONLY_BEFORE = \[Network-log\]
TRANSFORM-network = network_custom_fields

### Task 5: What is the captured domain?

By inspecting the raw log data provided in the challenge, I quickly identified the domain that users were accessing: Cybertees.THM.

To complete the rest of the tasks, I needed to extract the custom fields. I used the raw log text and a tool like regex101.com to build a comprehensive regex pattern that successfully captured all required fields (User, Department, Resource, IP, and Country).

The resulting regex was placed in the transforms.conf file:

[network_custom_fields]
REGEX = \[Network-log\]:\sUser\snamed\s([\w\s]+)\sfrom\s([\w]+)\sdepartment\saccessed\sthe\sresource\s([\w]+\.[\w]+\/[\w-]+\.[\w]+)\sfrom\sthe\ssource\sIP\s((?:[0–9]{1,3}\.){3}[0–9]{1,3})\sand\scountry\s*([\w\s+)(?=\s+at:)
FORMAT = Username::$1 Department::$2 Resource::$3 SourceIP::$4 Country::$5

[Screenshot 02: Regex pattern in transforms.conf](./screenshots/02-transforms-conf.png)

Finally, I defined the new fields in fields.conf and restarted Splunk:

[Resource]
[Country]
[Username]
[Department]
[SourceIP]

[Screenshot 03: Fields defined in fields.conf](./screenshots/03-fields-conf.png)

/opt/Splunk/bin/splunk restart

### Task 6: How many countries are captured in the logs?

After restarting Splunk and searching for events with sourcetype=network_logs, the extracted fields immediately became visible. I clicked on the Country field to see the unique count.

[Screenshot 04: Count of captured countries](./screenshots/04-countries-count.png)

### Task 7: How many departments are captured in the logs?

By checking the count for the newly extracted Department field, I determined the number of unique departments accessing the resource.

### Task 8: How many usernames are captured in the logs?

I checked the unique count for the Username field.

### Task 9: How many source IPs are captured in the logs?

I checked the unique count for the SourceIP field.

 ### Task 10: Which configuration files were used to fix our problem? [Alphabetic order: File1, file2, file3]
 
 The three configuration files required to fix the event parsing, extract the fields, and make the fields searchable were: fields.conf, props.conf, transforms.conf.
 
 ### Task 11: What are the TOP two countries the user Robert tried to access the domain from? [Answer in comma-separated and in Alphabetic Order][Format: Country1, Country2]
 
 I ran a targeted search to filter logs for the user Robert and then used a statistical command to group and count the Country field.
 
 sourcetype=network_logs Username="Robert" | top Country
 
 The top two countries were Canada, United States.
 
 ### Task 12: Which user accessed the secret-document.pdf on the website?
 
 I performed a targeted search for the specific file name in the Resource field.
 
 sourcetype=network_logs Resource="Cybertees.THM/secret-document.pdf"
 
 The resulting event clearly showed the Username responsible for accessing the document.
 
 [Screenshot 05: User accessing secret-document.pdf](./screenshots/05-user-secret-document.png)
 
 ---
 
 # Summary
 
 The Fixit challenge provided excellent practical experience in advanced Splunk data modeling. The key takeaways were:
 
 -Event Boundary Correction: Correctly using SHOULD_LINEMERGE = true and BREAK_ONLY_BEFORE in props.conf to handle unstructured, multi-line logs.
 -Custom Field Extraction: Writing complex regular expressions in transforms.conf to parse multiple field values from a single event string.
 -Configuration Synergy: Understanding the interplay between props.conf (defining the source and pointing to transformations), transforms.conf (defining the regex logic), and fields.conf (making the fields searchable).
 -Forensic Analysis: Leveraging the newly extracted fields to quickly and accurately answer specific forensic questions (e.g., counting unique entities, identifying top access locations, and tracking file access).
 
 This hands-on exercise reinforced the concept that proper log parsing is the foundation of effective SIEM analysis.