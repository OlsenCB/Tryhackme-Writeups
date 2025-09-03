# TryHackMe – Classic Passwd

## Room Description
The **Classic Passwd** room on TryHackMe is a small reverse engineering challenge.  
The goal is to analyze a provided ELF binary, understand how it validates input, and finally retrieve the hidden flag.  
This task focuses on dynamic analysis rather than complex reversing — a perfect way to practice with tools like `ltrace`.

---

## Steps I Took

### 1. Making the binary executable
After downloading the challenge file, I first checked its properties and made it executable:

chmod +x Challenge_1609966715991.Challenge
 ./Challenge_1609966715991.Challenge

[Screenshot 1: Running the binary for the first time](./screenshots/01-run-binary.png)

### 2. Dynamic analysis with ltrace

To better understand how the program checks the username, I ran it with ltrace:

ltrace ./Challenge_1609966715991.Challenge

This revealed a function call comparing my input with the expected value.
At this point, I identified where the check happens. (Sensitive parts of the output are blurred to respect THM rules.)

[Screenshot 2: ltrace output showing the validation process](./screenshots/02-ltrace.png)

### 3. Providing the correct input

After discovering the validation logic, I executed the program again and entered the correct username.
This time the program accepted it and displayed the flag.
Both the username and the flag are censored in the screenshot to stay compliant with TryHackMe's guidelines.

[Screenshot 3: Successful authentication and flag](./screenshots/03-flag.png)

---

## Lessons Learned

- ltrace is a powerful tool to trace library calls in real time.

- Even without static reversing, you can uncover validation logic by simply monitoring program behavior.

- Always respect challenge rules: hide sensitive data (usernames, flags) when sharing write-ups.

---

## Summary

Challenge Type: Reverse engineering / dynamic analysis

Key Tool: ltrace

Result: Successfully retrieved the flag

Screenshots: 3 (execution, analysis, flag)
