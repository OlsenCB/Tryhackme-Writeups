# TryHackMe – Memory Forensics

## Room Description
The **Memory Forensics** room on TryHackMe focuses on analyzing memory dumps using **Volatility**.  
The tasks involve extracting password hashes, analyzing shutdown times, reviewing user console history, and retrieving sensitive information such as TrueCrypt passwords.

---

## Steps I Took

### 1. Extracting John's password hash
I started with the provided file `Snapshot6.vmem`. First, I identified the memory profile:

volatility -f Snapshot6.vmem imageinfo

The profile was detected as Win7SP1x64. Using this, I dumped password hashes:

volatility -f Snapshot6.vmem --profile=Win7SP1x64 hashdump

From the output, I retrieved John’s NTLM hash:

aad3b435b51404eeaad3b435b51404ee:47fbd6536d7868c873d5ea455f2fc0c9


I saved the hash to a file and used John the Ripper with the RockYou wordlist to crack it:

./john --wordlist=/usr/share/wordlists/rockyou.txt --format=NT hash.txt

After a short time, John the Ripper successfully recovered the password.


[Screenshot 1: Cracked password for John](./Screenshots/01-john-password.png)

### 2. Finding last shutdown time

The next task required analyzing another memory file: Snapshot19.vmem.
I again confirmed the profile:

volatility -f Snapshot19.vmem imageinfo

Profile: Win7SP1x64.
To retrieve the last shutdown time, I ran:

volatility -f Snapshot19.vmem --profile=Win7SP1x64 shutdowntime

[Screenshot 2: Last shutdown time](./Screenshots/02-shutdown-time.png)

### 3. Reviewing John’s command history

On the same snapshot (Snapshot19.vmem), I looked for console history:

volatility -f Snapshot19.vmem --profile=Win7SP1x64 consoles

From the results, one interesting command stood out: John saved a flag into test.txt.

[Screenshot 3: Console history](./Screenshots/03-console-history.png)

### 4. Extracting TrueCrypt password

For the last part, I analyzed another file: Snapshot14.vmem.
Since the profile was the same, I directly ran:


volatility -f Snapshot14.vmem --profile=Win7SP1x64 truecryptpassphrase


The output revealed the stored TrueCrypt passphrase.

[Screenshot 4: TrueCrypt password](./Screenshots/04-truecrypt-pass.png)

---

## Lessons Learned

Volatility is a powerful tool for memory forensics and supports a wide range of plugins.

Identifying the correct profile (imageinfo) is always the first and crucial step.

Password hashes can be extracted and cracked with tools like John the Ripper.

Memory analysis also reveals shutdown events, user activity, and even encrypted container keys.

---

## Summary

Challenge Type: Memory forensics

Tools used: Volatility, John the Ripper

Key Findings:

	John’s cracked password

	System last shutdown time

	John’s console history

	TrueCrypt passphrase

Screenshots: 4 (password crack, shutdown time, console history, TrueCrypt password)
