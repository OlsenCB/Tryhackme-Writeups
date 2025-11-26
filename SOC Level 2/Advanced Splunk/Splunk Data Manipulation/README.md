# TryHackMe Room: Splunk: Data Manipulation

This room moved beyond basic searches in Splunk and dove into the critical process of data manipulation and configuration. I learned how to use essential configuration files (props.conf, transforms.conf) to normalize unstructured data, manage event boundaries, mask sensitive information, and perform custom field extractions. This is crucial for making logs actually useful in a security context.

---

## Section: Scenario and Lab Instruction

### Task 2: How many Python scripts are present in the ~/Downloads/scripts directory?

After accessing the target machine, I navigated to the specified directory and counted the files with the .py extension. There were 3 Python scripts present.

[Screenshot 01: Python scripts count](./screenshots/01-python-scripts-count.png)

---

## Section: Exploring Splunk Configuration files

### Task 1: Which stanza is used in the configuration files to break the events after the provided pattern?

The stanza used to force an event break after a specific pattern in configuration files like props.conf is BREAK_ONLY_AFTER.

### Task 2: Which stanza is used to specify the pattern for the line break within events?

The stanza that defines the regular expression pattern used by Splunk to identify where an event should break is LINE_BREAKER.

### Task 3: Which configuration file is used to define transformations and enrichments on indexed fields?

The configuration file responsible for defining how data fields are transformed, enriched, and looked up is transforms.conf.

### Task 4: Which configuration file is used to define inputs and ways to collect data from different sources?

The file that specifies which directories Splunk monitors and how it collects data from different sources is inputs.conf.

---

## Section: Creating a Simple Splunk App

### Task 1: If you create an App on Splunk named THM, what will be its full path on this machine?

When an app named THM is created on a standard Splunk installation on this machine, its files and configurations will reside at: /opt/splunk/etc/apps/THM

---

## Section: Event Boundaries - Understanding the problem

### Task 1: Which configuration file is used to specify parsing rules?

The primary configuration file used to specify parsing rules, including how Splunk should handle event breaks and line merging, is props.conf.

### Task 2: What regex is used in the above case to break the Events?

In the example given in the room, the regular expression used to explicitly match the connection status and define event boundaries was (DISCONNECT|CONNECT).

### Task 3: Which stanza is used in the configuration to force Splunk to break the event after the specified pattern?

If you need to ensure an event must break after a specified pattern, the stanza to use is MUST_BREAK_AFTER.

### Task 4: If we want to disable line merging, what will be the value of the stanza SHOULD_LINEMERGE?

To disable Splunk's default behavior of merging multiple lines into a single event, the value for the SHOULD_LINEMERGE stanza must be set to false.

---

## Section: Parsing Multi-line Events

### Task 1: Which stanza is used to break the event boundary before a pattern is specified in the above case?

To ensure a new event begins immediately before a specific pattern is encountered in the log stream, the stanza to use is BREAK_ONLY_BEFORE.

### Task 2: Which regex pattern is used to identify the event boundaries in the above case?

In the provided example for handling multi-line authentication logs, the regex pattern used to mark the start of a new event was \[Authentication\].

---

## Section: Masking Sensitive Data

### Task 1: Which stanza is used to break the event after the specified regex pattern?

The stanza used to force an event break after a matching pattern is MUST_BREAK_AFTER.

### Task 2: What is the pattern of using SEDCMD in the props.conf to mask or replace the sensitive fields?

The SEDCMD stanza uses the standard Stream Editor (sed) syntax to replace or mask data. The general pattern is: s/oldValue/newValue/g

---

## Section: Extracting Custom Fields

### Task 2: Extract the Username field from the sourcetype purchase_logs we worked on earlier. How many Users were returned in the Username field after extraction?

To perform the custom field extractions, I had to configure four files:

-inputs.conf: Configured Splunk to monitor the purchase.log file.
-props.conf: Defined the log source type (purchase_logs) and added stanzas for REPORT-username and REPORT-creditcard pointing to transforms.conf definitions.
-transforms.conf: Defined the regex for extracting Username and Credit-Card values.
-fields.conf: Defined the extracted fields.

After restarting Splunk (/opt/splunk/bin/splunk restart) and running the search sourcetype="purchase_logs", I checked the extracted fields. The Username field showed 8 unique users.

[Screenshot 02: inputs.conf configuration](./screenshots/02-inputs-conf.png)

[Screenshot 03: props.conf configuration](./screenshots/03-props-conf.png)

[Screenshot 04: transforms.conf configuration](./screenshots/04-transforms-conf.png)

[Screenshot 05: fields.conf configuration](./screenshots/05-fields-conf.png)

[Screenshot 06: Search results showing unique Users](./screenshots/06-search-results-users.png)

### Task 3: Extract Credit-Card values from the sourcetype purchase_logs, how many unique credit card numbers are returned against the Credit-Card field?

The search results for sourcetype="purchase_logs" initially showed 8 unique credit cards, but as the index finished processing, the total unique count of credit card numbers returned against the Credit-Card field was 16.

---

# Summary

This room provided crucial training in Splunk data governance and manipulation, which is vital for any security professional managing a SIEM. Key skills covered included:

-Configuration File Management: Understanding the roles of inputs.conf, props.conf, and transforms.conf in controlling data ingestion and parsing.
-Event Boundary Control: Using stanzas like BREAK_ONLY_BEFORE and MUST_BREAK_AFTER to ensure multi-line events are indexed correctly.
-Sensitive Data Masking: Employing the SEDCMD stanza with regular expressions for critical security tasks like redacting confidential information.
-Custom Field Extraction: Defining new fields in transforms.conf using regex to make valuable data (like usernames and credit card numbers) easily searchable and queryable in the Splunk interface.