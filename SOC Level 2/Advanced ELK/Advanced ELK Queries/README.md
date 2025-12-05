# TryHackMe Room: Advanced ELK Queries

This room was an essential exploration into the more sophisticated search capabilities within the Elastic Stack (Kibana Query Language, specifically). Moving beyond simple field-value matching, I learned to leverage advanced techniques such as nested queries, range operators, fuzzy matching, proximity searching, and regular expressions to perform highly targeted and complex forensic investigations on incident data.

---

## Section: A Primer on Advanced Queries

### Task 1: How do you escape the text "password:Me&Try=Hack!" (Not including the double quotes)

When querying, special characters like & and = must be escaped using a backslash (\) to prevent them from being interpreted as query operators.

The escaped query is: password:Me\&Try\=Hack!

### Task 2: Using wildcards, what will your query be if you want to search for all documents that contain the words "hacking" and "hack" in the "activity" field?

To capture all variations of the root word, I use the wildcard operator (*): activity:hack*

---

## Section: Nested Queries

### Task 1: How many incidents exist where the affected file is "marketing_strategy_2023_07_23.pptx"?

I started with a simple wildcard search on the file name within the Kibana search bar to find the relevant incidents.

Query: marketing_strategy_2023_07_23*

[Screenshot 01: Incident count for marketing_strategy_2023_07_23.pptx](./screenshots/01-marketing-strategy-count.png)

### Task 2: How many incidents exist where the affected files in file servers are titled "marketing_strategy"?

To target incidents involving a specific file pattern and restrict the search to only file servers, I combined two field-value pairs using the AND operator:

Query: affected_systems.affected_files.file_name:marketing_strategy* AND affected_systems.system_name:"file-server*"

[Screenshot 02: Incident count for file servers marketing_strategy](./screenshots/02-fileserver-marketing-count.png)

### Task 3: There is a true positive alert on a webserver where the admin and it users were logged on. What is the name of the webserver?

I constructed a specific nested query to pinpoint the server: System type must be "web server", a user named "admin" must be logged on, and the incident must be classified as a "true positive."

Query: affected_systems.system_type: "web server" AND affected_systems.logged_on_users: "admin" AND incident_comments: "true positive"

[Screenshot 03: Name of the web server (true positive admin)](./screenshots/03-web-server-name.png)

---

## Section: Ranges

### Task 1: How many "Data Leak" incidents have a severity level of 9 and up?

I used the greater than or equal to operator (>=) on the numerical severity_level field, combined with a search for the incident_type.

Query: severity_level >= 9 AND incident_type : "Data Leak"

[Screenshot 04: Count of Data Leak incidents severity >= 9](./screenshots/04-data-leak-severity-count.png)

### Task 2: How many incidents before December 1st, 2022 has AJohnston investigated where the affected system is either an Email or Web server?

I used a range query on the @timestamp field (<) to set the upper limit for the date and combined it with the team member's name and a multi-value system type search using OR (implied by listing values in parenthesis, or in this case, a space-separated list for simplicity).

Query: @timestamp<"2022-11-30" AND affected_systems.system_type : email OR web AND team_members.name : AJohnston

[Screenshot 05: Incident count before date 2022-12-01](./screenshots/05-incidents-before-date.png)

### Task 3: From the incident IDs 1 to 500, what is the email address of the SOC Analyst that left a comment on an incident that the data leak on file-server-65 is a false positive?

I used range operators (>= and <=) on the incident_id field, combined with targeted searches for the incident comment and system name.

Query: incident_id >= 1 AND incident_id <= 500 AND incident_comments : "false positive" AND affected_systems.system_name: "file-server-65"

[Screenshot 06: Email address of SOC Analyst (incident ID range)](./screenshots/06-analyst-email.png)

---

## Section: Fuzzy Searches

### Task 1: Including the misspellings, how many incidents has JLim handled where he misspelt the word “true”?

Fuzzy searching is used to find terms that are similar to the search term, typically by disabling KQL and using the Lucene query syntax. The tilde (~) operator followed by a number (edit distance) is used.

Query: team_members.name : JLim AND incident_comments:true~1

[Screenshot 07: Incident count JLim misspelled true](./screenshots/07-jlim-true-misspelled-count.png)

### Task 2: How many incidents has JLim handled where he misspelt the word “negative”?

I compared the count of exact matches for "negative" with a fuzzy search allowing for two differences (~2).

Exact matches: team_members.name : JLim AND incident_comments : "negative" (115 hits)

[Screenshot 08: True Negative](./screenshots/08-true-negative.png)

Fuzzy matches: team_members.name : JLim AND incident_comments : negative~2 (119 hits)

[Screenshot 09: Misspelled Negative](./screenshots/09-misspelled-negative.png)

The difference in the number of hits is 4 (119 - 115).

---

## Section: Proximity Searches

### Task 1: How many incidents are there when you want to look for the words "data leak" and "true negative" in the comments that are at least 3 words in between them?

Proximity searching uses the tilde (~) operator at the end of a phrase to specify the maximum distance (word count) between the words in the phrase.

Query: incident_comments: "data leak true negative"~3

[Screenshot 10: Incident count proximity search data leak true negative](./screenshots/10-proximity-search-count.png)

### Task 2: How many incidents has AJohnston investigated that have the words "detected" and "negative" in the comments that are two words apart?

I combined the team member filter with a proximity search on the comments, setting the maximum word distance to ~2.

Query: team_members.name : "AJohnston" AND incident_comments: "detected negative"~2

[Screenshot 11: Incident count AJohnston detected negative proximity](./screenshots/11-ajohnston-proximity-count.png)

---

## Section: Regular Expression

### Task 1: How many incidents are there where a "client_list" file was affected by ransomware?

To search for a broad pattern that includes the word "ransomware" (or a variation), I used the RegEx operator (/pattern/) and combined it with the specific file name.

Query: incident_comments: /(r).*/ AND affected_systems.affected_files.file_name: client_list*

[Screenshot 12: Incident count client_list ransomware regex](./screenshots/12-client-list-regex-count.png)

### Task 2: What is the name of the affected system at the earliest incident date that EVenis investigated with a filename containing the word "project"?

I searched for the user EVenis and any file containing the term "project". To find the earliest incident, I sorted the results by @timestamp in ascending order and inspected the first hit.

Query: affected_systems.affected_files.file_name: project* AND team_members.name: EVenis

[Screenshot 13: Affected system name earliest incident EVenis project](./screenshots/13-earliest-incident-affected-system.png)

---

# Summary

This room was crucial for developing expert-level querying skills in the Elastic Stack. I successfully implemented:

-Targeted Filtering: Utilizing nested field structures and range operators (>=, <, [1 TO 500]) to pinpoint specific incidents.
-Fuzzy and Proximity Matching: Employing the Lucene syntax (tilde ~) to compensate for misspellings and to enforce precise word distances, which is invaluable in threat hunting.
-Regex Integration: Using regular expressions (/pattern/) to search for broad, complex patterns within log fields.

These advanced techniques allow for a much higher degree of precision and speed when investigating large-scale data in a security environment.