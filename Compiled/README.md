# TryHackMe – Compiled

## Room Description
The **Compiled** room on TryHackMe is a reverse engineering challenge.  
The goal is to analyze the given binary file, understand how it processes the input, and discover the correct password to solve the challenge.

---

## Steps I Took

### 1. Making the binary executable
After downloading the file `Compiled.Compiled`, I made it executable and ran it:

chmod +x Compiled.Compiled
./Compiled.Compiled

The program asked for a password. I tried a random input (e.g. text), but the password was invalid.

[Screenshot 1: Running the binary for the first time](./screenshots/01-run-binary.png)

### 2. Static analysis with Ghidra

To understand the binary, I opened it in Ghidra and navigated to the main function.

[Screenshot 2: main function in Ghidra](./screenshots/02-main-function.png)

From the decompiled code, I noticed the following logic:

The program asks for a password in the format:
DoYouEven%sCTF

The input is stored in a local variable.

It compares the extracted string with "__dso_handle". If equal, it prints "Try again!" and exits.

It then compares the extracted string with "_init". If equal, the password is accepted.

This means the input must be crafted so that the local variable equals "_init".
The way the format string works (__isoc99_scanf("DoYouEven%sCTF", local_28)) is that it takes anything typed between DoYouEven and CTF.

Some examples:

DoYouEvenanytextCTF → input is anytext

DoYouEven_initCTF → input is _init

Therefore, the correct password string is:
DoYouEven_initCTF


### 3. Providing the correct input

I ran the program again, this time entering the discovered password.
The program accepted it and confirmed success.

[Screenshot 3: Correct password entered](./screenshots/03-correct-password.png)

---

## Lessons Learned

- Reverse engineering with Ghidra makes it easier to identify how user input is processed.

- Understanding format strings (e.g. scanf("%s")) is crucial to spotting how passwords are extracted.

- Crafting the input to match the expected comparison revealed the correct solution.

---

## Summary

Challenge Type: Reverse engineering / binary analysis

Tools used: Ghidra

Key Finding: The program extracts text between DoYouEven and CTF and checks if it equals _init.

Solution: DoYouEven_initCTF

Screenshots: 3 (binary execution, Ghidra main function, correct password)
