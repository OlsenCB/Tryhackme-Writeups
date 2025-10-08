# TryHackMe Room: Friday Overtime

In this room I investigated shared malware samples, extracted hashes, mapped them to MITRE ATT&CK, and analyzed malicious infrastructure using **CyberChef** and **VirusTotal**.

---

## 1. Who shared the malware samples?
After starting the VM and opening **split view**, I used **Chromium** to visit the portal provided in the task and logged in with the given credentials.  

[Screenshot 01: Login credentials](./screenshots/01-credentials.png)  

On the home page, the portal clearly shows who shared the samples.  

[Screenshot 02: Shared by](./screenshots/02-shared-by.png)

---

## 2. What is the SHA1 hash of the file `pRsm.dll` inside `samples.zip`?
I navigated to **Urgent: Malicious Artefacts Detected**, downloaded the `samples.zip` file, and extracted it using the password provided in the email.  
Then I calculated the SHA1 hash with:

sha1sum pRsm.dll

[Screenshot 03: SHA1 hash of pRsm.dll](./screenshots/03-sha1.png)

---

## 3. Which malware framework utilizes these DLLs as add-on modules?
Searching online for `pRsm.dll malware framework` led me to this report:  
[WeLiveSecurity – Evasive Panda APT Group](https://www.welivesecurity.com/2023/04/26/evasive-panda-apt-group-malware-updates-popular-chinese-software/)  

Scrolling to **Key points of the report**, the last bullet point mentions the malware framework.  

[Screenshot 04: Framework reference](./screenshots/04-framework.png)

---

## 4. Which MITRE ATT&CK Technique is linked to using `pRsm.dll` in this malware framework?
On the same page, searching for `pRsm.dll` showed a section under **Collection** with the linked ATT&CK Technique.  

[Screenshot 05: MITRE ATT&CK Collection technique](./screenshots/05-collection.png)

---

## 5. What is the CyberChef defanged URL of the malicious download location first seen on 2020-11-02?
Searching for `2020-11-02` in the report revealed the malicious URL.  
I then used **CyberChef → Defang URL** to convert it into its safe representation.  

[Screenshot 06: Defanged URL in CyberChef](./screenshots/06-defanged-url.png)

---

## 6. What is the CyberChef defanged IP address of the C&C server first detected on 2020-09-14 using these modules?
Searching for `2020-09-14` showed the IP address in the **Network** section.  
I used **CyberChef → Defang IP** to safely present it.  

[Screenshot 07: Defanged IP address](./screenshots/07-defanged-ip.png)

---

## 7. What is the MD5 hash of the SpyAgent spyware hosted on the same IP targeting Android devices in Jun 2025?
Using the IP address in **VirusTotal**, I navigated to the **Relations** tab, then clicked the related Android sample.  
In **Details**, the MD5 hash of the SpyAgent family spyware was displayed.  

[Screenshot 08: MD5 hash in VirusTotal](./screenshots/08-md5.png)

---

## Summary
In this room I practiced:
- Extracting file hashes (`sha1sum`) from suspicious DLLs.  
- Researching malware frameworks and linked ATT&CK techniques.  
- Using **CyberChef** to defang malicious indicators (URLs, IPs).  
- Pivoting into **VirusTotal** to analyze infrastructure and related malware.  

This was a great exercise in combining static file analysis with open-source intelligence to trace malware samples across different platforms.
