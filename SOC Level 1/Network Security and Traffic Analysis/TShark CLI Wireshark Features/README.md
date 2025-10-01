# TryHackMe Room: TShark – CLI Wireshark Features

In this room I learned how to use TShark – the command-line version of Wireshark. All the challenges are based on several `.pcap` files that are available on the attached VM. Below I go step by step through what I did and what I found.

---

## Section: Command-Line Wireshark Features I | Statistics

### Task 1: What is the byte value of the TCP protocol?
To answer this, I first started the machine (since it contains all the necessary `.pcap` files). I navigated into the `exercise-files` folder and ran:
tshark -r write-demo.pcap -z io,phs -q

[TCP Value](./screenshots/01_tcp_value.png)

### Task 2: In which packet lengths row is our packet listed?

The command for this one is:
tshark -r write-demo.pcap -z plen,tree -q
On the screenshot below you can see that only one row shows our packet length, and that’s exactly the answer.

[Packet Row](./screenshots/02_packet_row.png)

### Task 3: What is the summary of the expert info?

Again, I used TShark to display expert info:
tshark -r write-demo.pcap -z expert -q

[Expert Summary](./screenshots/03_expert_summary.png)

### Task 4: List the communications. What is the IP address that exists in all IPv4 conversations?

Here it’s not enough to just run the command – we also need to process the output with CyberChef to make a defanged IP.
tshark -r demo.pcapng -z conv,ip -q

From the output we can see that one IP repeats three times. That’s our suspicious one.
[Repeated IPs](./screenshots/04_repeated_ips.png)

Now I took that IP and ran it through CyberChef to create a defanged version.
[Defanged IP #1](./screenshots/05_defanged_ip1.png)

## Section: Command-Line Wireshark Features II | Specific Filters for Particular Protocols

### Task 1: Which IP address has 7 appearances?

Here I used:
tshark -r demo.pcapng -z ip_hosts,tree -q

Only one IP address had Count == 7.
[IP Count 7](./screenshots/06_ip_count7.png)

Then, of course, I defanged it with CyberChef.
[Defanged IP #2](./screenshots/07_defanged_ip2.png)

### Task 2: What is the "destination address percentage" of the previous IP?

This time I reused the IP from the previous task and ran:
tshark -r demo.pcapng -z ip_srcdst,tree -q

On the screenshot you can clearly see the percentage values.
[Destination Percentages](./screenshots/08_destination_percentages.png)

### Task 3: Which IP address constitutes "2.33% of the destination addresses"?

The answer is visible on the same output as before. I just copied the IP and converted it into a defanged format in CyberChef.
[Defanged IP #3](./screenshots/09_defanged_ip3.png)

### Task 4: What is the average "Qname Len" value?

This one is simpler – just run:
tshark -r demo.pcapng -z dns,tree -q

[Qname Len](./screenshots/10_qname_len.png)

## Section: Command-Line Wireshark Features III | Streams, Objects and Credentials

### Task 1: Follow the "UDP stream 0". What is the "Node 0" value?

To check this, I ran:
tshark -r demo.pcapng -z follow,udp,ascii,0 -q

The IP I got then had to be defanged in CyberChef. (Or you can just manually insert [] around the dots if you’re lazy).
[Node 0](./screenshots/11_node0.png)

### Task 2: Follow the "HTTP stream 1". What is the "Referer" value?

This one hides under the Referer field:
tshark -r demo.pcapng -z follow,http,ascii,1 -q

[Referer](./screenshots/12_referer.png)

But since it’s a URL, I also defanged it with CyberChef.
[Defanged URL](./screenshots/13_defanged_url.png)

### Task 3: What is the total number of detected credentials?

Here we switched to another file credentials.pcap. First I tried:
tshark -r credentials.pcap -z credentials -q | wc -l

That gave me 79, but that’s not correct. With the hint I realized we need to subtract banners. Using:
tshark -r credentials.pcap -z credentials -q | nl

I saw that entries 1, 2, 3 and 79 are banners. So the real math is 79 - 4 = 75.
[Credentials Packets 1](./screenshots/14_credentials_pkt1.png)
[Credentials Packets 2](./screenshots/15_credentials_pkt2.png)

## Section: Advanced Filtering Options | Contains, Matches and Extract Fields

### Task 1: What is the HTTP packet number that contains the keyword "CAFE"?

Here’s the command:
tshark -r demo.pcapng -Y 'http contains "CAFE"'

[CAFE Packet](./screenshots/16_cafe_packet.png)

### Task 2: Filter packets with GET and POST requests and extract the packet frame time.
The command is long, but works perfectly:
tshark -r demo.pcapng -Y 'http.request.method == "GET" || http.request.method == "POST"' -T fields -e frame.time

[First Timestamp](./screenshots/17_first_timestamp.png)

## Section: Use Cases | Extract Information

### Task 1: What is the total number of unique hostnames?

Switching to hostnames.pcapng, I used:
tshark -r hostnames.pcapng -T fields -e dhcp.option.hostname | awk NF | sort -r | uniq -c | sort -r | wc -l

[Unique Hostnames](./screenshots/18_unique_hostnames.png)

### Task 2: What is the total appearance count of the "prus-pc" hostname?

This time:
tshark -r hostnames.pcapng -T fields -e dhcp.option.hostname | grep -c "prus-pc"

[prus-pc Count](./screenshots/19_pruspc_count.png)

### Task 3: What is the total number of queries of the most common DNS query?

Now using dns-queries.pcap:
tshark -r dns-queries.pcap -T fields -e dns.qry.name | sort | uniq -c | sort -nr | head -n 1

[DNS Queries Count](./screenshots/20_dns_queries.png)

### Task 4: What is the total number of the detected "Wfuzz user agents"?

Final .pcap is user-agents.pcap:
tshark -r user-agents.pcap -Y 'http.user_agent contains "Wfuzz"' | wc -l

[Wfuzz User Agents](./screenshots/21_wfuzz.png)

### Task: What is the "HTTP hostname" of the nmap scans?

I ran:
tshark -r user-agents.pcap -T fields -e http.host

From the output one IP repeated many times – clear sign of the nmap scan.
[Nmap Scan Host](./screenshots/22_nmap_scan.png)

Then I defanged it with CyberChef for the final answer.
[Defanged Last IP](./screenshots/23_defanged_last_ip.png)

---

## Lessons Learned

- Using `tshark` with statistics options (`-z`) provides quick insights into packet distributions, protocol usage, and expert information.  
- Combining `tshark` with CyberChef makes it simple to generate defanged IPs and URLs for safe reporting.  
- Filtering with expressions (`-Y`) allows precise extraction of packets, such as specific flags, HTTP methods, or user agents.  
- Counting and sorting outputs with tools like `awk`, `grep`, and `wc -l` is key to extracting unique values from packet captures.  
- Not all results are correct at first glance (e.g., credential count included banners), so verifying with deeper inspection is essential.  

---

## Summary

**Challenge Type:** Network forensics / CLI packet analysis  
**Tools Used:** TShark, CyberChef, Linux utilities (`awk`, `grep`, `wc`, `sort`)  
**Key Findings:**  
- Identified protocol values, packet statistics, and IP conversations across multiple `.pcap` files.  
- Extracted suspicious IPs and URLs, converting them into defanged formats for reporting.  
- Detected credentials from traffic captures after filtering out non-relevant entries.  
- Found repeated patterns in DNS queries, user agents, and hostnames.  
- Traced HTTP and UDP streams to reveal sensitive metadata like Referer values and node addresses.  
