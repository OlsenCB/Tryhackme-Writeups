\# TryHackMe Room: Brim



This room introduced me to Brim, a powerful desktop application that combines the power of Zeek with a graphical user interface. By using a query language, Brim allows for quick and efficient analysis of pcap files, making it a great tool for security investigations and threat hunting. After a brief overview, I started the sections to get hands-on experience.



---



\## Section: The Basics



\### Task 1: Process the "sample.pcap" file and look at the details of the first DNS log that appear on the dashboard. What is the "qclass\_name"?





After launching Brim on the machine, I opened the sample.pcap file. I filtered the dashboard to show only the DNS logs (\_path:dns) and clicked on the very first log entry. The details panel on the right showed me the value of the qclass\_name field.



\[Screenshot 01: DNS qclass\_name](./screenshots/01-dns-qclass-name.png)



\### Task 2: Look at the details of the first NTP log that appear on the dashboard. What is the "duration" value?



I performed a similar action, but this time I filtered the logs by \_path:ntp to find the NTP log entries. Clicking on the first one, I found the duration field in the details panel.



\[Screenshot 02: NTP duration value](./screenshots/02-ntp-duration.png)



\### Task 3: Look at the details of the STATS packet log that is visible on the dashboard. What is the "reassem\_tcp\_size"?



Once again, I filtered the logs, this time for \_path:stats. After selecting the first entry, I found the reassem\_tcp\_size field in the details pane.



\[Screenshot 03: STATS reassem\_tcp\_size](./screenshots/03-stats-reassem-tcp-size.png)



---



\## Section: Default Queries



\### Task 1: Investigate the files. What is the name of the detected GIF file?



I opened a new pcap file, task4-sample-b.pcap, to start this section. Brim has many useful built-in queries. I used the "File Activity" query to filter the logs and find the detected file name.



\[Screenshot 04: Detected GIF file](./screenshots/04-detected-gif.png)



\### Task 2: Investigate the conn logfile. What is the number of the identified city names?



This task required a custom query, as there wasn't a pre-made one for it. I filtered for connection logs and used the cut command to select specific geographical fields, then sorted the results. I manually counted the unique city names from the output.



\_path=="conn" | cut geo.resp.country\_code, geo.resp.region, geo.resp.city | sort geo.resp.city



\[Screenshot 05: City names query](./screenshots/05-city-names-query.png)



\### Task 3: Investigate the Suricata alerts. What is the Signature id of the alert category "Potential Corporate Privacy Violation"?



Brim provides pre-made queries for Suricata alerts. I selected the "Suricata Alerts by category" query. After locating the "Potential Corporate Privacy Violation" category, I right-clicked it and selected "Pivot to Logs" to view the detailed logs and find the Signature ID.



\[Screenshot 06: Suricata alerts by category](./screenshots/06-suricata-alerts-category.png)



---



\## Section: Exercise: Threat Hunting with Brim | Malware C2 Detection



\### Task 1: What is the name of the file downloaded from the CobaltStrike C2 connection?



From the room's provided information, I learned that the IP address 104.168.44.45 was associated with a CobaltStrike C2 server. To find the downloaded file, I filtered the HTTP logs and cut the relevant fields to find a unique entry that stood out.



\_path=="http" | cut host, method, uri | uniq



\[Screenshot 07: Downloaded file from C2](./screenshots/07-c2-downloaded-file.png)



\### Task 2: What is the number of CobaltStrike connections using port 443?



To count the connections, I filtered for conn logs, then used cut to view the response port and host. I piped the result to a count and a unique command to get the final number.



\_path=="conn" | cut id.resp\_p, id.resp\_h | 104.168.44.45 | uniq -c



\[Screenshot 08: CobaltStrike connections count](./screenshots/08-cobaltstrike-connections.png)



\### Task 3: There is an additional C2 channel in used the given case. What is the name of the secondary C2 channel?



For this task, I used a hint from the room's walkthrough, which provided a useful query to find the secondary C2 channel. The query analyzed the alerts and sorted them by signature to find the one with the highest count.



event\_type=="alert" | cut alert.signature | sort -r | uniq -c | sort -r count



\[Screenshot 09: Secondary C2 channel](./screenshots/09-secondary-c2-channel.png)



The query's result pointed to IcedID.



---



\## Section: Exercise: Threat Hunting with Brim | Crypto Mining



\### Task 1: How many connections used port 19999?



I switched to the task7 pcap file for this section. I filtered the connection logs for port 19999 and used the count() function to get the total number of connections.



\_path=="conn" id.resp\_p==19999 | count()



\[Screenshot 10: Connections on port 19999](./screenshots/10-port-19999-connections.png)



\### Task 2: What is the name of the service used by port 6666?



To find the service name, I filtered the connection logs for port 6666 and used cut to view the service field.



\_path=="conn" id.resp\_p==6666 | cut id.resp\_p, service | uniq -c



\[Screenshot 11: Service on port 6666](./screenshots/11-port-6666-service.png)



\### Task 3: What is the amount of transferred total bytes to "101.201.172.235:8888"?



This query required me to filter for both the specific host and port and then use the sum() function to add up the orig\_bytes and resp\_bytes.



\_path=="conn" id.resp\_p==8888 id.resp\_h==101.201.172.235 | sum(orig\_bytes + resp\_bytes)



\[Screenshot 12: Transferred bytes sum](./screenshots/12-transferred-bytes-sum.png)



\### Task 4: What is the detected MITRE tactic id?



I used Brim's pre-built "Suricata Alerts by Category" query again. I located the "Crypto Currency Mining Activity Detected" category, right-clicked, and pivoted to logs. The mitre\_technique\_id field provided the answer.



\[Screenshot 13: MITRE tactic id](./screenshots/13-mitre-tactic-id.png)



---



\## Summary

This room was a comprehensive introduction to Brim, a tool that simplifies packet analysis. I learned how to:



Navigate the Brim GUI to inspect various log types (dns, ntp, stats).



Use Brim's query language to filter, cut, and analyze specific log fields.



Utilize built-in queries for common tasks like file activity and Suricata alerts.



Craft custom queries for specific threat hunting scenarios, such as C2 detection and crypto mining.



Pivot from high-level alerts to detailed logs to find specific indicators of compromise (IOCs).



Overall, Brim is a highly effective tool that streamlines the process of network traffic analysis, making threat hunting much more efficient.

