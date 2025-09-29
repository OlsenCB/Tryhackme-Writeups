# TryHackMe Room: TShark – The Basics

In this room I practiced using **capinfos** and **tshark** to analyze packet captures and filter traffic. Below are the tasks with the commands used and answers.

---

## 1. View the details of the demo.pcapng file with `capinfos`.  
**Task:** What is the "RIPEMD160" value?  

capinfos demo.pcapng

[Screenshot 01: capinfos output with RIPEMD160](./screenshots/01-capinfos.png)

---

## 2. Installed TShark version  
**Task:** What is the installed TShark version in the given VM?  

tshark -v

[Screenshot 02: TShark version](./screenshots/02-version.png)

---

## 3. List available interfaces  
**Task:** What is the number of available interfaces in the given VM?  

sudo tshark -D

[Screenshot 03: Available interfaces](./screenshots/03-interfaces.png)

---

## 4. Read the `demo.pcapng` file  
**Task:** What are the assigned TCP flags in the 29th packet?  

tshark -r demo.pcapng

[Screenshot 04: TCP flags in packet 29](./screenshots/04-tcp-flags.png)

---

## 5. Ack value of the 25th packet  
No extra command was needed — I checked the output of the previous `tshark -r` command.  

[Screenshot 05: ACK value in packet 25](./screenshots/05-ack.png)

---

## 6. Window size value of the 9th packet  
Also visible directly from the earlier output.  

[Screenshot 06: Window size of packet 9](./screenshots/06-window-size.png)

---

## 7. Parameter for continuous capture  
From the docs segment:  
Answer → `-b`

---

## 8. Combine autostop and ring buffer?  
Answer → `y`

---

## 9. Parameter to set Capture Filters  
Answer → `-f`

---

## 10. Parameter to set Display Filters  
Answer → `-Y`

---

## 11. Number of packets with SYN bytes  

tshark -r demo.pcapng -Y "tcp.flags.syn == 1" | wc -l

[Screenshot 07: SYN packets count](./screenshots/07-syn.png)

---

## 12. Number of packets sent to IP `10.10.10.10`  

tshark -r demo.pcapng -Y "ip.dst == 10.10.10.10" | wc -l

[Screenshot 08: Packets sent to 10.10.10.10](./screenshots/08-ip-10-10-10-10.png)

---

## 13. Number of packets with ACK bytes  

tshark -r demo.pcapng -Y "tcp.flags.ack == 1" | wc -l
Answer: **8**

---

## 14. Number of packets with IP `65.208.228.223`  

tshark -r demo.pcapng -Y "ip.addr == 65.208.228.223" | wc -l

[Screenshot 09: Packets with IP 65.208.228.223](./screenshots/09-ip-65-208.png)

---

## 15. Number of packets with TCP port 3371  

tshark -r demo.pcapng -Y "tcp.port == 3371" | wc -l


[Screenshot 10: Packets on TCP port 3371](./screenshots/10-port-3371.png)

---

## 16. Number of packets with IP `145.254.160.237` as source  

tshark -r demo.pcapng -Y "ip.src == 145.254.160.237" | wc -l

[Screenshot 11: Source packets from 145.254.160.237](./screenshots/11-ip-145-254.png)

---

## 17. Duplicate packet number  

tshark -r demo.pcapng -Y "ip.src == 145.254.160.237 and udp.analysis.duplicate" -T fields -e frame.number
Answer: **37**

---

## Summary
In this room I practiced:
- Using `capinfos` to extract file-level metadata.  
- Checking TShark version and interfaces.  
- Reading packet captures and analyzing flags, ACK values, and window sizes.  
- Applying **capture filters (`-f`)** and **display filters (`-Y`)**.  
- Counting packets based on IPs, ports, and TCP flags.  
- Identifying duplicate packets by frame number.  

This gave me a strong foundation in packet analysis using **TShark**.
