# TryHackMe Room: Zeek

This room provided a hands-on introduction to Zeek (formerly known as Bro), a powerful network security monitoring tool. I learned how to use Zeek to analyze packet capture files (.pcap) and parse the resulting logs to find key information. The tasks involved using a variety of commands and scripts to extract data efficiently.

---

## Network Security Monitoring and Zeek

### 2. What is the installed Zeek instance version number?

To check the Zeek and ZeekControl versions, I used the following commands.
zeek -v
zeekctl -v

[Screenshot 01: Zeek versions](./screenshots/01-zeek-versions.png)

### 3. What is the version of the ZeekControl module?

The answer to this question can be found in the output of the previous command

### 4. Investigate the "sample.pcap" file. What is the number of generated alert files?

I navigated to the Exercise-Files/TASK-2 folder and ran the zeek command on the sample.pcap file. This generated several log files, and I counted the number of them.

zeek -C -r sample.pcap

[Screenshot 02: Number of generated files](./screenshots/02-generated-files.png)

---

## Zeek Logs

### 2. Investigate the sample.pcap file. Investigate the dhcp.log file. What is the available hostname?

I navigated to the TASK-3 folder and ran the Zeek command to generate the necessary logs. Then, I used zeek-cut to parse the dhcp.log file and display only the host_name field.

zeek -C -r sample.pcap
cat dhcp.log | zeek-cut host_name

[Screenshot 03: DHCP hostname](./screenshots/03-dhcp-hostname.png)

### 3. Investigate the dns.log file. What is the number of unique DNS queries?

To find the number of unique DNS queries, I used zeek-cut to extract the query field, then piped the output to uniq -c to count the unique entries.

cat dns.log | zeek-cut query | uniq -c

[Screenshot 04: Unique DNS queries](./screenshots/04-dns-queries.png)

### 4. Investigate the conn.log file. What is the longest connection duration?

I used a combination of zeek-cut, sort, and head to find the longest connection duration. The -n flag sorts numerically, and -r sorts in reverse, so the longest duration appears first.

cat conn.log | zeek-cut duration | sort -n -r | head -n 1

[Screenshot 05: Longest connection duration](./screenshots/05-longest-duration.png)

---

## Zeek Signatures

### 2. Investigate the http.pcap file. Create the HTTP signature shown in the task and investigate the pcap. What is the source IP of the first event?

First, I went to the TASK-5/http folder and created a new signature file named http-password.sig using nano.

nano http-password.sig

[Screenshot 06: HTTP password signature](./screenshots/06-http-password-sig.png)

Then, I ran the Zeek command with the -s flag to apply the signature to the http.pcap file. This generated signatures.log. I then parsed this log to find the source IP.

zeek -C -r http.pcap -s http-password.sig
cat signatures.log | zeek-cut src_addr | sort -r

[Screenshot 07: Source IP from signature log](./screenshots/07-signature-src-ip.png)

### 3. What is the source port of the second event?

I used the same signatures.log and simply changed the zeek-cut field to src_port to find the source port of the second event.

cat signatures.log | zeek-cut src_port | sort

[Screenshot 08: Source port from signature log](./screenshots/08-signature-src-port.png)

### 4. Investigate the conn.log. What is the total number of the sent and received packets from source port 38706?

I used zeek-cut to extract the id.orig_p, orig_pkts, and resp_pkts fields from the conn.log file. Then, I used grep to filter for the specific port 38706. The answer is the sum of the two numbers in the output.

cat conn.log | zeek-cut id.orig_p orig_pkts resp_pkts | grep 38706

[Screenshot 09: Packet count for port 38706](./screenshots/09-packet-count.png)

### 5. Create the global rule shown in the task and investigate the ftp.pcap file. Investigate the notice.log. What is the number of unique events?
Now we need to modify ftp-bruteforce.sig:

[Screenshot 10: ftp-bruteforce.sig](./screenshots/10-ftp-bruteforce.png)

After navigating to the ftp folder and modifying the ftp-bruteforce.sig file, I ran the Zeek command and then checked the notice.log for unique events.

zeek -C -r ftp.pcap -s ftp-bruteforce.sig
cat notice.log | zeek-cut uid | sort | uniq | wc -l

[Screenshot 11: Unique events count](./screenshots/11-unique-events.png)

### 6. What is the number of ftp-brute signature matches?

First, I checked the fields in the signatures.log file using head. Knowing that the relevant field is sig_id, I then used zeek-cut and grep to find and count the matches.

cat signatures.log | head -n 20
cat signatures.log | zeek-cut sig_id | grep "ftp-brute" | wc -l

[Screenshot 12: Signature matches count](./screenshots/12-signature-matches.png)

---

## Zeek Scripts | Fundamentals

### 2. Investigate the smallFlows.pcap file. Investigate the dhcp.log file. What is the domain value of the "vinlap01" host?

I went to the TASK-6 folder, ran the standard Zeek command, and then used zeek-cut on dhcp.log to find the domain.

zeek -C -r smallFlows.pcap
cat dhcp.log | zeek-cut domain

[Screenshot 13: DHCP domain value](./screenshots/13-dhcp-domain.png)

### 3. Investigate the bigFlows.pcap file. Investigate the dhcp.log file. What is the number of identified unique hostnames?

I repeated the process in the bigFlows.pcap folder to count the unique hostnames. It's important to remember that the first line of the output is empty, so I had to subtract one from the final count.

zeek -C -r bigFlows.pcap
cat dhcp.log | zeek-cut host_name | sort | uniq | wc -l

[Screenshot 14: Unique hostnames count](./screenshots/14-unique-hostnames.png)

### 4. Investigate the dhcp.log file. What is the identified domain value?

I used a similar command as in Task 2, but added sort and uniq to display only the unique domain names.

cat dhcp.log | zeek-cut domain | sort | uniq

[Screenshot 15: Unique domain values](./screenshots/15-unique-domains.png)

---

## Zeek Scripts | Scripts and Signatures

### 2. Go to folder TASK-7/101. Investigate the sample.pcap file with 103.zeek script. Investigate the terminal output. What is the number of the detected new connections?

I ran the zeek command with the script file as an argument. Then, I counted the unique UIDs in the conn.log file.

zeek -C -r sample.pcap 103.zeek
cat conn.log | zeek-cut uid | sort | uniq | wc -l

[Screenshot 16: New connections count](./screenshots/16-connections-count.png)

### 3. Go to folder TASK-7/201. Investigate the ftp.pcap file with ftp-admin.sig signature and 201.zeek script. Investigate the signatures.log file. What is the number of signature hits?

I used a single command to run the Zeek script with the signature and count the number of resulting log entries.

zeek -C -r ftp.pcap 201.zeek -s ftp-admin.sig | wc -l

[Screenshot 17: Signature hits count](./screenshots/17-signature-hits.png)

### 4. Investigate the signatures.log file. What is the total number of "administrator" username detections?

I checked the fields using head and then used zeek-cut and grep to count the detections of the "administrator" username.

cat signatures.log | zeek-cut sub_msg | grep "USER administrator" | wc -l

[Screenshot 18: Administrator detections count](./screenshots/18-administrator-detections.png)

### 5. Investigate the ftp.pcap file with all local scripts, and investigate the loaded_scripts.log file. What is the total number of loaded scripts?

I ran the Zeek command with the local argument to load all scripts, which generated the loaded_scripts.log. I then counted the scripts listed in that log.

zeek -C -r ftp.pcap 201.zeek local
cat loaded_scripts.log | zeek-cut name | wc -l

[Screenshot 19: Loaded scripts count](./screenshots/19-loaded-scripts.png)

### 6. Go to folder TASK-7/202. Investigate the ftp-brute.pcap file with "/opt/zeek/share/zeek/policy/protocols/ftp/detect-bruteforcing.zeek" script. Investigate the notice.log file. What is the total number of brute-force detections?

I ran the Zeek command with the provided script path. The output generated a notice.log file, where I counted the "FTP::Bruteforcing" entries.

zeek -C -r ftp-brute.pcap /opt/zeek/share/zeek/policy/protocols/ftp/detect-bruteforcing.zeek
cat notice.log | zeek-cut note | grep "FTP::Bruteforcing" | wc -l

Answer: 2

---

## Zeek Scripts | Frameworks

### 2. Investigate the case1.pcap file with intelligence-demo.zeek script. Investigate the intel.log file. Look at the second finding, where was the intel info found?

After running Zeek with the intelligence-demo.zeek script, I used zeek-cut to find the seen.where field in the intel.log file.

zeek -C -r case1.pcap intelligence-demo.zeek
cat intel.log | zeek-cut seen.where

[Screenshot 20: Intel location](./screenshots/20-intel-location.png)

### 3. Investigate the http.log file. What is the name of the downloaded .exe file?

I used zeek-cut to get the uri field from http.log and then grep with a regular expression (\.exe$) to find a file ending with .exe. The backslash \ escapes the dot, and the $ ensures the match is at the end of the line.

cat http.log | zeek-cut uri | grep '\.exe$'

[Screenshot 21: Downloaded .exe file](./screenshots/21-exe-file.png)

### 4. Investigate the case1.pcap file with hash-demo.zeek script. Investigate the files.log file. What is the MD5 hash of the downloaded .exe file?

I ran Zeek with the hash-demo.zeek script and then used zeek-cut to get the mime_type and md5 fields from files.log, revealing the hash of the downloaded .exe file.

zeek -C -r case1.pcap hash-demo.zeek
cat files.log | zeek-cut mime_type md5

[Screenshot 22: MD5 hash of the .exe file](./screenshots/22-md5-hash.png)

### 5. Investigate the case1.pcap file with file-extract-demo.zeek script. Investigate the "extract_files" folder. Review the contents of the text file. What is written in the file?

Running Zeek with this script extracts files from the .pcap and places them in a new extract_files folder. I then used cat to view the content of the extracted text file.

zeek -C -r case1.pcap file-extract-demo.zeek
cat extract_files/*

[Screenshot 23: Content of extracted file](./screenshots/23-extracted-file.png)

---

## Zeek Scripts | Packages

### 2. Investigate the http.pcap file with the zeek-sniffpass module. Investigate the notice.log file. Which username has more module hits?

I went to the TASK-9 folder and ran Zeek with the zeek-sniffpass package. Then, I used zeek-cut, uniq, and c on the notice.log to count the unique messages and find which username had the most hits.

zeek -C -r http.pcap zeek-sniffpass
cat notice.log | zeek-cut msg | uniq -c

[Screenshot 24: Username hits](./screenshots/24-username-hits.png)

### 3. Investigate the case2.pcap file with geoip-conn module. Investigate the conn.log file. What is the name of the identified City?

I ran Zeek with the geoip-conn module. The grep -v -e "-" part of the command is important as it excludes entries that show a '-' in the geoip fields, which means no location data was available.

zeek -C -r case2.pcap geoip-conn
cat conn.log | zeek-cut id.resp_h geo.resp.city | grep -v -e "-" | uniq -c

[Screenshot 25: GeoIP city](./screenshots/25-geoip-city.png)

### 4. Which IP address is associated with the identified City?

The IP address is visible in the output of the previous command.

### 5. Investigate the case2.pcap file with sumstats-counttable.zeek script. How many types of status codes are there in the given traffic capture?

Finally, I ran Zeek with the sumstats-counttable.zeek script and used a complex one-liner to parse the output, count the unique status codes, and get the final answer.

zeek -C -r case2.pcap sumstats-counttable.zeek | awk '{print $3}' | grep -v -e '^$' | sort | uniq | wc -l

[Screenshot 26: Status codes count](./screenshots/26-status-codes.png)

---

## Summary
In this room, I practiced:

Using Zeek to read and analyze packet capture (.pcap) files.

Generating and parsing Zeek logs (conn.log, dns.log, http.log, etc.) using the zeek-cut utility.

Manipulating and filtering data with standard command-line tools like sort, uniq, wc, and grep.

Creating and applying signatures (.sig files) to detect specific patterns in network traffic.

Utilizing pre-built Zeek scripts and packages to perform advanced analysis, such as brute-force detection, file extraction, and geolocation.

This room provided a comprehensive overview of Zeek's capabilities, from basic log analysis to advanced threat hunting.
