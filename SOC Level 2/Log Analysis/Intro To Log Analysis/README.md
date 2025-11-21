# TryHackMe Room: Intro to Log Analysis

This room provided a fundamental introduction to the world of Log Analysis, covering both theoretical concepts and practical techniques using various tools. Understanding how to collect, consolidate, and analyze logs is crucial for threat hunting and incident response. This writeup details the steps taken using CLI tools, regular expressions, and specialized utilities to analyze log data.

---

## Section: Investigation Theory

### Task 1: What's the term for a consolidated chronological view of logged events from diverse sources, often used in log analysis and digital forensics?

The section content explicitly defines this concept in its first line:
A super timeline, also known as a consolidated timeline, is a powerful concept in log analysis and digital forensics. Super timelines provide a comprehensive view of events across different systems, devices, and applications, allowing analysts to understand the sequence of events holistically.

### Task 2: Which threat intelligence indicator would 5b31f93c09ad1d065c0491b764d04933 and 763f8bdbc98d105a8e82f36157e98bbe be classified as?

Since the provided values are long, hexadecimal strings of fixed length, they are identifiable as file hashes.

---

## Section: Nginx & Log Basics

### Task 1: What is the default file path to view logs regarding HTTP requests on an Nginx server?

The default path for HTTP access logs on an Nginx server is: /var/log/nginx/access.log

### Task 2: A log entry containing %2E%2E%2F%2E%2E%2Fproc%2Fself%2Fenviron was identified. What kind of attack might this infer?

The presence of URL-encoded characters like %2E (which decodes to .) and %2F (which decodes to /) suggests an attacker is attempting to bypass security filters. This pattern points to a Path Traversal attack, where the attacker is trying to access files outside the intended web root directory.

---

## Section: Automated vs Manual Analysis

### Task 1: A log file is processed by a tool which returns an output. What form of analysis is this?

When a tool handles the heavy lifting of processing and summarizing data, the analysis is classified as Automated.

### Task 2: An analyst opens a log file and searches for events. What form of analysis is this?

When a human directly inspects and searches a log file, the analysis is classified as Manual.

---

## Section: Log Analysis Tools: Command Line

For this section, I downloaded the provided apache.log file and used standard Linux CLI tools for analysis.

### Task 1: Use cut on the apache.log file to return only the URLs. What is the flag that is returned in one of the unique entries?

I used cut to isolate the 7th field (the URL/Request target) using a space ( ) as the delimiter. I then sorted the unique URLs to find the flag hidden in one of the entries.

cut -d ' ' -f 7 apache.log | sort | uniq

[Screenshot 01: Unique flag from URLs](./screenshots/01-unique-flag.png)

### Task 2: In the apache.log file, how many total HTTP 200 responses were logged?

I used the awk command to filter for log entries where the 9th field (the HTTP response code) was exactly 200. I then piped the result to wc -l to count the total lines.

awk '$9 == 200' apache.log | wc -l

[Screenshot 02: Count of HTTP 200 responses](./screenshots/02-http-200-count.png)

### Task 3: In the apache.log file, which IP address generated the most traffic?

To find the top communicating IP, I used cut to extract the first field (the IP address). I then sorted them, used uniq -c to count duplicate entries, sorted the results numerically in reverse (-nr), and finally used head -1 to show only the top result.

cut -d ' ' -f 1 apache.log | sort | uniq -c | sort -nr | head -1

[Screenshot 03: IP address with most traffic](./screenshots/03-top-traffic-ip.png)

### Task 4: What is the complete timestamp of the entry where 110.122.65.76 accessed /login.php?

I used grep to quickly filter the log file based on both the IP address and the requested page to find the specific log entry.

grep "110.122.65.76" apache.log | grep "/login.php"

[Screenshot 04: Complete timestamp of specific entry](./screenshots/04-specific-timestamp.png)

---

## Section: Log Analysis Tools: Regular Expressions

### Task 1: How would you modify the original grep pattern above to match blog posts with an ID between 20-29?

The original pattern was post=1\[0-9], which matches IDs from 10 to 19. To match IDs between 20 and 29, I changed the leading digit from 1 to 2.

The modified pattern is: post=2\[0-9]

### Task 2: What is the name of the filter plugin used in Logstash to parse unstructured log data?

The filter plugin used in Logstash for parsing unstructured log data is Grok.

---

## Section: Log Analysis Tools: Cyberchef

### Task 2: Upload the log file named "access.log" to CyberChef. Use regex to list all of the IP addresses. What is the full IP address beginning in 212?

I uploaded access.log to CyberChef and used the Extract Regular Expression operation with a standard IPv4 regex pattern:

\b(\[0-9]{1,3}\.){3}\[0-9]{1,3}\b

I selected the List matches option and searched the output for the IP address starting with 212.

[Screenshot 05: IP address starting with 212 from CyberChef](./screenshots/05-cyberchef-ip-212.png)

### Task 3: Using the same log file from Question #2, a request was made that is encoded in base64. What is the decoded value?

First, I used grep to extract potential Base64 strings from the access.log file by looking for sequences of at least 20 Base64 characters (A-Z, a-z, 0-9, +, /, =).

grep -Eo '\[A-Za-z0-9+/=]{20,}' access.log

[Screenshot 06: Extracted Base64 encoded string](./screenshots/06-extracted-base64-string.png)

I took the resulting string and fed it into CyberChef, applying the From Base64 recipe to get the decoded value.

[Screenshot 07: Decoded Base64 value from CyberChef](./screenshots/07-decoded-base64.png)

### Task 4: Using CyberChef, decode the file named "encodedflag.txt" and use regex to extract by MAC address. What is the extracted value?

I uploaded encodedflag.txt to CyberChef. After decoding, I applied the Extract MAC addresses recipe, which automatically identified and extracted the MAC address from the decoded text.

[Screenshot 08: Extracted MAC address](./screenshots/08-extracted-mac.png)

---

## Section: Log Analysis Tools: Yara and Sigma

### Task 1: What languages does Sigma use?

Sigma uses the YAML syntax for defining its rules.

### Task 2: What keyword is used to denote the "title" of a Sigma rule?

The keyword used to denote the title of a Sigma rule is title.

### Task 3: What keyword is used to denote the "name" of a rule in YARA?

The keyword used to denote the name of a rule in YARA is rule.

---

## Summary

This room served as an essential introduction to the methodologies and tools used in real-world log analysis. I gained hands-on experience in:

-CLI Analysis: Mastering essential command-line utilities like cut, awk, and grep for quick data filtering, aggregation, and pattern matching directly within large log files.
-Regular Expressions (RegEx): Understanding how to craft and apply precise RegEx patterns for complex searching, which is critical for identifying specific, often malicious, activities.
-Specialized Tools: Utilizing powerful, dedicated tools such as CyberChef for encoding/decoding, string manipulation, and pattern extraction (like MAC addresses and IP addresses).
-Threat Intelligence Frameworks: Learning the fundamental syntax and purpose of rule engines like YARA (for malware identification) and Sigma (for SIEM/Log detection), which are used to automate the detection of threats.

The exercise underscored the importance of integrating various tools and techniques to efficiently transform vast amounts of unstructured log data into actionable security intelligence.
