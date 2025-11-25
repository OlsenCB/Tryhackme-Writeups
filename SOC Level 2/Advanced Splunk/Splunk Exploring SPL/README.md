# TryHackMe Room: Splunk: Exploring SPL

This room provided a practical, hands-on introduction to the Splunk Search Processing Language (SPL). The goal was to understand how to navigate the Splunk interface, use core search commands, and filter, structure, and transform log data to extract specific security and operational intelligence from Windows event logs.

---

## Section: Connect with the Lab

### Task 2: What is the name of the host in the Data Summary tab?

After connecting to the lab machine and navigating to the Splunk search interface, I executed a broad search (index=* or index="windowslogs") and checked the Data Summary tab. By clicking on Hosts, I identified the single host being indexed.

[Screenshot 01: Hostname in Data Summary](./screenshots/01-hostname-summary.png)

--- 

## Section: Search & Reporting App Overview

### Task 1: In the search History, what is the 7th search query in the list? (excluding your searches from today)

I navigated to the search bar and expanded the Search History panel. After filtering out my recent searches, I identified the 7th query in the list.

[Screenshot 02: Search History list](./screenshots/02-search-history.png)

### Task 2: In the left field panel, which Source IP has recorded max events?

I returned to the search results for index=windowslogs and inspected the left sidebar field panel. I expanded the filter options to view the available fields and clicked on the SourceIp field to see the distribution. The IP address with the highest number of recorded events was clearly displayed.

[Screenshot 03: Source IP with max events](./screenshots/03-max-source-ip.png)

### Task 3: How many events are returned when we apply the time filter to display events on 04/15/2022 and Time from 08:05 AM to 08:06 AM?

I used the time range picker above the search results to apply a precise filter for the specified date and minute range. This updated the event count displayed.

[Screenshot 04: Event count after time filter](./screenshots/04-time-filter-count.png)

---

## Section: Splunk Processing Language Overview

### Task 1: How many Events are returned when searching for Event ID 1 AND User as James?

I constructed a simple search query using boolean logic to find events that matched both EventID=1 and a User containing the term "James". I then used the stats command to get the total count.

index="windowslogs" EventID=1 User=*James* | stats count

[Screenshot 05: Event count for James](./screenshots/05-event-count-james.png)

## Task 2: How many events are observed with Destination IP 172.18.39.6 AND destination Port 135?

I modified the previous query to search for events matching a specific DestinationIp and DestinationPort.

index="windowslogs" DestinationIp=172.18.39.6 DestinationPort=135 | stats count

[Screenshot 06: Event count for specific IP and Port](./screenshots/06-event-count-ip-port.png)

### Task 3: What is the Source IP with highest count returned with this Search query?

I executed the provided search query and inspected the left-hand field panel for the SourceIp field. Splunk automatically calculates the event distribution for each field, making it easy to spot the IP with the highest count.

index=windowslogs Hostname="Salena.Adam" DestinationIp="172.18.38.5"

[Screenshot 07: Source IP with highest count for query](./screenshots/07-query-source-ip.png)

### Task 4: In the index windowslogs, search for all the events that contain the term cyber how many events returned?

I searched for the exact term "cyber". Since no events matched the precise string, the event count was zero.

index=windowslogs "cyber"

[Screenshot 08: Event count for 'cyber' (exact)](./screenshots/08-count-cyber.png)

### Task 5: Now search for the term cyber*, how many events are returned?

I updated the query to use a wildcard (*) after the term, searching for any string starting with "cyber" (e.g., "cybersecurity", "cyberattack"). This significantly increased the event count.

index=windowslogs "cyber*"

[Screenshot 09: Event count for 'cyber' (exact)](./screenshots/09-count-cyber2.png)

---

## Section: Filtering the Results in SPL

### Task 1: What is the third EventID returned against this search query?

I ran the provided search query which uses the reverse command to display the results starting from the newest event.

index=windowslogs | table _time EventID Hostname SourceName | reverse

[Screenshot 10: Third EventID with reverse](./screenshots/10-third-event-id.png)

### Task 2: Use the dedup command against the Hostname field before the reverse command in the query mentioned in Question 1. What is the first username returned in the Hostname field?

I modified the query to include the dedup Hostname command before reverse. This ensures that only the first unique entry for each hostname is retained, and the reverse command then orders the results.

index=windowslogs | table _time EventID Hostname SourceName | dedup Hostname | reverse

[Screenshot 11: First Hostname with dedup and reverse](./screenshots/11-dedup-reverse-hostname.png)

---

## Section: SPL - Structuring the Search Results

### Task 1: Using the Reverse command with the search query index=windowslogs | table _time EventID Hostname SourceName - what is the HostName that comes on top?

Running the query with the reverse command displays the most recent event first. The top entry in the Hostname field was easily identified.

### Task 2: What is the last EventID returned when the query in question 1 is updated with the tail command?

I updated the command, replacing reverse with tail, which shows the oldest events in the log.

index=windowslogs | table _time EventID Hostname SourceName | tail

[Screenshot 12: Last EventID with tail](./screenshots/12-last-event-id-tail.png)

### Task 3: Sort the above query against the SourceName. What is the top SourceName returned?

I changed the command to use the sort pipe command, ordering the results alphabetically by SourceName.

index=windowslogs | table _time EventID Hostname SourceName | sort SourceName

[Screenshot 13: Top SourceName with sort](./screenshots/13-top-sourcename-sort.png)

---

## Section: Transformational Commands in SPL

### Task 1: List the top 8 Image processes using the top command - what is the total count of the 6th Image?

I used the top command to calculate the frequency of the Image field and quickly identify the most common processes.

index=windowslogs | top limit=8 Image

[Screenshot 14: Count of 6th Image using top](./screenshots/14-top-image-count.png)

### Task 2: Using the rare command, identify the user with the least number of activities captured?

The rare command is the inverse of top, designed specifically to identify the least common values in a field. I applied it to the User field.

index=windowslogs | rare User

[Screenshot 15: User with least activities (rare)](./screenshots/15-least-user-rare.png)

### Task 3: Create a pie-chart using the chart command - what is the count for the conhost.exe process?

I used the chart command to summarize the event counts by the process Image. After running the search, I switched to the Visualization tab to view the results as a pie chart, where I could hover over the conhost.exe slice to find its count.

[Screenshot 16: Conhost.exe count from pie chart](./screenshots/16-conhost-pie-chart.png)

---

# Summary

This room was a highly effective practical exercise in mastering the fundamentals of Splunk's Search Processing Language (SPL). I learned how to:

-Filter Data: Use field-value pairs, boolean operators (AND), and time ranges for precise targeting.
-Wildcards: Understand the difference between exact matches (0 results) and using the wildcard operator (*) for broader searches.
-Structuring Results: Employ commands like table, reverse, tail, and dedup to control the display, order, and uniqueness of event data.
-Transformational Analysis: Utilize statistical and reporting commands like stats, top, rare, and chart to quickly summarize event frequencies and identify both common and uncommon activity across fields like Image and User.

These core SPL skills are essential for efficient log analysis and incident response in any environment using Splunk.