# Crafting a Trojan Virus in 5 Minutes

## Introduction
In this guide, I’ll walk you through creating a Trojan in just five minutes. We’ll use a familiar application, Putty (an SSH client for Windows), to trigger a calculator as a demonstration. You can replace the calculator with any payload you want — this is just for testing and learning purposes.

## What is a Trojan?
A Trojan, or Trojan horse, is malicious software disguised as legitimate code. It can seize control of your system, designed to harm, disrupt, steal data, or cause other damaging effects on your device or network.  
*Source: [us.norton.com](https://us.norton.com)*

> This tutorial draws from the Sektor7 RTO Essentials Course:  
> *RED TEAM Operator: Malware Development Essentials*  
> Learn ethical hacking, penetration testing, and red teaming through malware development.  
> *Source: [institute.sektor7.net](https://institute.sektor7.net)*

![image](https://github.com/user-attachments/assets/7bb04256-c447-434c-b2c3-ae9f495914a0)

## Backdooring an EXE
Here’s how to embed a backdoor in an executable file:

### 1. Locate a Code Cave
### 2. Insert a Memory Jump
### 3. Inject Your Shellcode

First, determine whether the target process is 32-bit or 64-bit. You can use Process Hacker to check this:

- **Download Process Hacker:**  
  A free, versatile tool for monitoring system resources, debugging, and detecting issues.  
  *Source: [processhacker.sourceforge.io](https://processhacker.sourceforge.io)*

Open `Putty.exe` in Process Hacker to inspect it:
![image](https://github.com/user-attachments/assets/e4745700-78af-41fb-8c17-86c197c39d66)

- Select `Putty.exe`
- Confirm it’s a 32-bit process
  
![image](https://github.com/user-attachments/assets/c42a593d-f82a-4aee-84fc-61da016d9d86)

Since it’s 32-bit, download `x32dbg` debugger (use `x64dbg` for 64-bit processes):

- **GitHub - x32dbg/x32dbg**  
  An open-source debugger for Windows, ideal for malware analysis and reverse engineering.  
  *Source: [github.com](https://github.com/x64dbg/x64dbg)*

Launch x32dbg:

- Go to **File -> Open -> Select Putty.exe**
  
![image](https://github.com/user-attachments/assets/2d58259e-e051-47f1-aebe-a4053afb77e9)

Result:

![image](https://github.com/user-attachments/assets/e70cfe54-a151-4d42-b84b-558579e417c0)

---

### Step 1: Find a Code Cave
Scroll through the memory in x32dbg to locate a block of empty memory (addresses filled with `00 00`). This is your **code cave**.

- Right-click the first empty memory address

![image](https://github.com/user-attachments/assets/d464f263-b59a-47df-afa9-38098747317b)

- Choose **Copy -> Address**, and save it in Notepad
  
![image](https://github.com/user-attachments/assets/348322bb-f04b-46c6-9730-d7647cebe663)

- Set a breakpoint: **Right-click -> Breakpoint -> Toggle**
  
![image](https://github.com/user-attachments/assets/43dc1a6a-b8ef-4a3f-8238-ea5f8c6f60ff)

- Navigate to the **Breakpoints** tab and select the **Entry breakpoint**

![image](https://github.com/user-attachments/assets/9f118be8-c39f-4ca0-a497-2312cd10cba4)

---

### Step 2: Create a Memory Jump
Modify the assembler instruction at the entry point:

- Right-click -> Assemble

![image](https://github.com/user-attachments/assets/7fadf251-cd7a-4cae-82ce-0bef8e902059)

- Enter:
```assembly
jmp 0xFirst0000Address
```
Reminder:

![image](https://github.com/user-attachments/assets/67c37047-3f60-4e77-9c36-f1c8d4534721)


This jump redirects the program’s execution to your shellcode in the empty memory when it starts.

Next, modify the assembler instructions for the first two empty memory addresses.

![image](https://github.com/user-attachments/assets/75b5da40-5c4d-4b4f-a292-d1a2c852952a)

![image](https://github.com/user-attachments/assets/82a566d9-f928-465b-9596-766df56d8464)

---

### Step 3: Inject Your Shellcode
You’ll need hexadecimal executable code for your payload.  
For this example, we use the calculator’s hex code:

![image](https://github.com/user-attachments/assets/3947bf5b-c80a-4c3f-b425-72a5c893a913)

> **calc32.hex**  
> *Source: ([https://drive.google.com](https://drive.google.com/file/d/1fICixdpAhY09QpP5zIe0RNwRdo1USvrU/view))*

- Select a large range of empty memory addresses in x32dbg
  
  ![image](https://github.com/user-attachments/assets/e78a5925-f337-499b-940f-640d62f1afa6)

- Right-click -> **Binary -> Edit** -> Paste the hex code (Ctrl+V)

  ![image](https://github.com/user-attachments/assets/3676f227-f9fc-4c34-849d-a7520a2a7397)

Your shellcode is now embedded.

- Right-click -> **Patches -> Select All -> Patch File**

![image](https://github.com/user-attachments/assets/82d86867-95df-4dd5-99cc-0517cd55d85b)

Run the modified executable, and the calculator should launch.

![image](https://github.com/user-attachments/assets/11afdf06-5cf3-4de8-82bd-6a949c64d0b4)

---

### Restoring Program Flow
To ensure Putty still functions, add a jump back to the original program flow after your shellcode executes.

1. Copy the memory address immediately following the entry point (increment the entry point address by one).
   
  ![image](https://github.com/user-attachments/assets/dde7c07e-82bb-4035-b000-305d740ec251)

2. Locate the last memory address used by your shellcode.
   
  ![image](https://github.com/user-attachments/assets/e959c51d-7c2f-4136-a454-72d2ac441e00)

3. Add an assembler instruction to jump back to the address from step 1.
   
  ![image](https://github.com/user-attachments/assets/f7f45738-0603-4d5c-94d9-0f45f2fbeb1e)

Run the executable again. The calculator should launch, followed by Putty’s normal operation.

![image](https://github.com/user-attachments/assets/9dca34db-71ae-4064-991f-bb1b4afd6e5f)

---

## Conclusion
This tutorial demonstrates how hackers craft Trojans.  
I hope you found it insightful and educational.  
Thanks for reading!

— **Malforge Group**

---
