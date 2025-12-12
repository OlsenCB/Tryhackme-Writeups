# TryHackMe Room: Slingshot

This room presented a highly realistic threat emulation scenario, requiring me to analyze a simulated cyber attack using security logs. The primary objective was to trace the attacker's steps from initial reconnaissance to web shell deployment, database compromise, and data exfiltration by querying and filtering events based on IP addresses, User Agents, response codes, and request bodies.

---

### Task 1: What was the attacker's IP?

To identify the attacker, I first configured the search results to display key fields relevant to web traffic analysis: response.status, http.url, request.headers.User-Agent, and transactions.remote_address. I set the time range to start on July 26, 2023.

By checking the transactions.remote_address field for event volume, one IP stood out with an extremely high number of events (2565), indicating heavy automated activity.

The attacker's IP was 192.168.1.18.

[Screenshot 01: Attacker's highest event count IP address](./screenshots/01-attacker-ip.png)

### Task 2: What was the first scanner that the attacker ran against the web server?

I filtered the logs using the attacker's IP address and sorted the events chronologically (oldest first). The very first activity recorded was a scan, identified by its User Agent.

The first scanner used was nmap.

[Screenshot 02: First scanner (nmap) used by attacker](./screenshots/02-first-scanner-nmap.png)

### Task 3: What was the User Agent of the directory enumeration tool that the attacker used on the web server?

Directory enumeration attempts often result in HTTP 404 (Not Found) errors. I filtered the logs by the attacker's IP and response.status: 404 and examined the User-Agent field for repeating, automated requests.

The User Agent of the enumeration tool was Mozilla/5.0 (Gobuster).

[Screenshot 03: User Agent of the directory enumeration tool (Gobuster)](./screenshots/03-dir-enum-gobuster.png)

### Task 4: In total, how many requested resources on the web server did the attacker fail to find?

The total number of failed requests corresponds exactly to the count of events with a 404 status code filtered by the attacker's IP. This count was visible from the search results after applying the filters from Task 3.

### Task 5: What is the flag under the interesting directory the attacker found?

After the enumeration phase, the attacker would attempt to access valid resources (HTTP 200 OK). I changed the filter to response.status: 200 to find successful requests. Analyzing these successful requests quickly revealed a path containing a flag.

[Screenshot 04: Flag found after successful resource access](./screenshots/04-successful-flag.png)

### Task 6: What login page did the attacker discover using the directory enumeration tool?

I removed the 404 and 200 status filters and searched for other response codes, specifically looking for common authentication response codes like 401 (Unauthorized). I found a URL ending in a login page that returned a 401 status.

The discovered login page was /admin-login.php.

[Screenshot 05: Admin login page returning 401 status](./screenshots/05-admin-login-401.png)

### Task 7: What was the user agent of the brute-force tool that the attacker used on the admin panel?

Since the attacker had found a login page, the next logical step was brute-forcing. I filtered for the login URL and the status code 401, which would show numerous failed attempts. I checked the User-Agent field within these failed attempts.

The User Agent of the brute-force tool was Hydra.

[Screenshot 06: User Agent of the brute-force tool (Hydra)](./screenshots/06-brute-force-hydra.png)

### Task 8: What username:password combination did the attacker use to gain access to the admin page?

The successful login attempt would have a 200 status code and the Hydra User Agent. I checked the request.headers.Authorization field of this single successful event. The value was Base64 encoded.

Upon decoding the Base64 string, the credentials were admin:thx1138.

[Screenshot 07: Base64 encoded credentials in Authorization header](./screenshots/07-encoded-login.png)

### Task 9: What flag was included in the file that the attacker uploaded from the admin directory?

After gaining access, the attacker likely uploaded a malicious file. I searched for all activity within the admin directory: http.url : /admin/*. I filtered for an event related to a file upload and examined the request.body field, which contained the flag.

[Screenshot 08: Flag included in the uploaded file request body](./screenshots/08-uploaded-file-flag.png)

### Task 10: What was the first command the attacker ran on the web shell?

The upload event (Task 9) showed the filename of the webshell (e.g., easy-simple-php-webshell-php). I searched for subsequent requests to this file with a 200 status code, sorted by the earliest time, to see the first command executed.

The first command the attacker ran was whoami.

[Screenshot 09: First command (whoami) run on the web shell](./screenshots/09-first-command-whoami.png)

##3 Task 11: What file location on the web server did the attacker extract database credentials from using Local File Inclusion?

Reviewing the web shell activity again, I looked for commands or requests indicative of Local File Inclusion (LFI). I found a request where the attacker successfully extracted sensitive configuration files.

The file location was /etc/phpmyadmin/config-db.php.

[Screenshot 10: LFI path used to extract database credentials](./screenshots/10-lfi-path.png)

### Task 12: What directory did the attacker use to access the database manager?

The path found in the previous task (/etc/phpmyadmin/config-db.php) directly pointed to the directory used to access the database management tool.

The directory was phpmyadmin.

### Task 13: What was the name of the database that the attacker exported?

I searched for all activity related to the database manager: http.url: /phpmyadmin/*. I specifically looked for export.php requests, as these are used for data exfiltration. The request.body of the relevant export.php event contained the name of the database being exported.

The exported database was customer_credit_cards.

[Screenshot 11: Database name exported during exfiltration](./screenshots/11-exported-database.png)

### Task 14: What flag does the attacker insert into the database?

Finally, I looked for database modification actions, specifically events related to import.php or tbl_replace.php. An event related to import.php contained a SQL query in the request body that included an INSERT INTO statement with the final flag in the VALUES field.

The final flag was c6aa3215a7d519eeb40a660f3b76e64c.

[Screenshot 12: Flag inserted into the database via import.php](./screenshots/12-inserted-flag.png)

