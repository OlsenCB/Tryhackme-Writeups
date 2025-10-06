# TryHackMe Room: TShark Challenge II: Directory

This room was a great exercise in using TShark, the command-line version of Wireshark, to perform a network forensics investigation. I practiced parsing a pcap file to identify a malicious domain, analyze its traffic, and extract a suspicious executable. The final steps involved using an external tool, VirusTotal, for further threat analysis.

---

## TShark Investigation

### 1. What is the name of the malicious/suspicious domain?

Enter your answer in a defanged format.

To begin the analysis, I navigated to the exercise-files folder. The first step was to identify the domains present in the packet capture. I used tshark to read the pcap file, filtered the output for DNS query names, and then used awk and uniq to get a list of unique domain names with their counts.

tshark -r directory-curiosity.pcap -T fields -e dns.qry.name | awk NF | uniq -c

[Screenshot 01: DNS query names](./screenshots/01-dns-qry-names.png)

The most queried domain was the suspicious one. I then "defanged" it by replacing the dots with brackets to neutralize it, making it safe to share.

### 2. What is the total number of HTTP requests sent to the malicious domain?

To count the total number of HTTP requests sent to the malicious domain, I used a tshark filter (-Y) to match the full_uri field and then piped the output to wc -l to count the lines.

tshark -r directory-curiosity.pcap -Y 'http.request.full_uri contains "jx2-bavuong.com"' -T fields -e http.request.full_uri | wc -l

[Screenshot 02: HTTP requests count](./screenshots/02-http-requests-count.png)

### 3. What is the IP address associated with the malicious domain?

Enter your answer in a defanged format.

To find the IP address, I filtered the DNS queries for Type 1 (A records), which map a domain name to an IPv4 address. I used grep to filter for the specific malicious domain and display its associated IP address.

tshark -r directory-curiosity.pcap -Y 'dns.qry.type == 1' -T fields -e dns.qry.name -e dns.a | awk NF | uniq -c | grep "jx2-bavuong.com"

[Screenshot 03: Malicious IP address](./screenshots/03-malicious-ip.png)

### 4. What is the server info of the suspicious domain?

I used tshark to display the server information for all HTTP traffic. By counting the unique occurrences, I could identify the server hosting the suspicious domain.

tshark -r directory-curiosity.pcap -T fields -e http.server | awk NF | uniq -c

[Screenshot 04: Server info](./screenshots/04-server-info.png)

### 5. Follow the "first TCP stream" in "ASCII". What is the number of listed files?

I used the tshark follow stream functionality (-z) to view the first TCP stream in plain ASCII format.

tshark -r directory-curiosity.pcap -z follow,tcp,ascii,0 -q

By carefully analyzing the output, I was able to find a directory listing of three files: 123.php, vlauto.exe, and vlauto.php.

[Screenshot 05: TCP stream file count](./screenshots/05-tcp-stream-files.png)

### 6. What is the filename of the first file?

Enter your answer in a defanged format.

Based on the output from the previous task, the first file listed was 123.php. Its defanged format is 123[.]php.

### 7. Export all HTTP traffic objects. What is the name of the downloaded executable file?

Enter your answer in a defanged format.

From my previous analysis of the TCP stream, I already knew the name of the downloaded executable file was vlauto.exe. Its defanged version is vlauto[.]exe.

### 8. What is the SHA256 value of the malicious file?

To obtain the file's hash, I first used tshark to export all HTTP objects from the packet capture into a directory. Then, I used the sha256sum command on the extracted vlauto.exe file.

tshark -r directory-curiosity.pcap --export-objects http,/home/ubuntu/Desktop/extracted-by-tshark -q
sha256sum vlauto.exe

[Screenshot 06: SHA256 hash of vlauto.exe](./screenshots/06-sha256-hash.png)

### 9. Search the SHA256 value of the file on VirusTotal. What is the "PEiD packer" value?

I navigated to the VirusTotal website and searched for the SHA256 hash I just found. On the file's page, under the Details tab, I located the PEiD packer field to find the answer.

[Screenshot 07: VirusTotal PEiD packer](./screenshots/07-vt-peid-packer.png)

### 10. Search the SHA256 value of the file on VirusTotal. What does the "Lastline Sandbox" flag this as?

Staying on the same VirusTotal page, I went to the Behavior tab. By navigating to the Dynamic Analysis Sandbox Detections section, I found the analysis by "Lastline Sandbox" and the corresponding flag.

[Screenshot 08: VirusTotal Lastline Sandbox](./screenshots/08-vt-lastline-sandbox.png)

---

## Summary
In this room, I practiced:

Using TShark to filter and analyze network traffic based on various fields (dns.qry.name, http.request.full_uri, http.server, etc.).

Following and interpreting TCP streams to reconstruct communication content.

Exporting HTTP objects from a pcap file for further analysis.

Calculating file hashes with sha256sum.

Using VirusTotal to gain threat intelligence on a suspicious file, including its packer information and sandbox analysis results.

This challenge provided valuable experience in performing a structured, end-to-end network forensics investigation.

