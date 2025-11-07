# TryHackMe — *Pickle Rick* (Professional Walkthrough)

**Sources:**

* TryHackMe room: [https://tryhackme.com/room/picklerick](https://tryhackme.com/room/picklerick)
* External writeup referenced: Ahmed Hilal — *TryHackMe CTF: Pickle Rick — Walkthrough*

---

## Objective

Retrieve **three ingredients** required for Rick’s potion. The machine exposes a web service and an admin panel; the goal is to enumerate the service, gain code execution, and escalate to root to collect all flags.

---

## 1 — Reconnaissance

### 1.1 Initial TCP scan

A quick `nmap` scan to discover open ports and services:

```bash
nmap -sC -sV -oN nmap_scan 10.10.141.239
```

**Key findings:**

* **Port 22** — OpenSSH (likely not directly exploitable without credentials).
* **Port 80** — Apache HTTP server hosting a website → primary attack surface.

Focus is on the HTTP service (port 80).

---

## 2 — Web enumeration

### 2.1 Visit the site

Browse `http://10.10.141.239`. The site is simple (Rick & Morty themed). Inspect the HTML source for hidden information.

**Hidden comment discovered**:

```html
<!--
Note to self, remember username!
Username: R1ckRul3s
-->
```

This yields a plausible username: `R1ckRul3s`.

### 2.2 Directory/file discovery

Run a directory brute-force to find hidden endpoints:

```bash
gobuster dir -u http://10.10.141.239 -w /usr/share/wordlists/dirb/common.txt -x php,txt,html
```

**Found paths:**

* `/assets/` — static files (images, JS, CSS)
* `/login.php` — admin login page

### 2.3 robots.txt and credential material

Check `robots.txt`:

```
http://10.10.141.239/robots.txt
```

**Content discovered:**

```
Wubbalubbadubdub
```

This string appears as a strong candidate for a password (likely intentionally left in robots.txt).

---

## 3 — Authentication & initial access

### 3.1 Try discovered credentials

Use the username and candidate password:

* **Username:** `R1ckRul3s`
* **Password:** `Wubbalubbadubdub`

These credentials successfully log into the admin panel, which exposes functionality allowing command execution (a command panel).

---

## 4 — Remote command execution (limited shell)

### 4.1 Conduct basic enumeration

Using the command panel, run standard reconnaissance commands:

```bash
whoami
pwd
ls -la
```

Result: access to a limited shell running as `www-data`.

### 4.2 Check user directories

List `/home`:

```bash
ls /home
```

**Users present:** `rick`, `ubuntu`

Inspect Rick’s home directory:

```bash
ls /home/rick
```

A file named (or referencing) `second ingredient` (or similar) is found—one of the three ingredients.

> Note: `cat` may be blacklisted; use allowed utilities (e.g., `grep . file`) to read contents.

Example to read a file when `cat` is blocked:

```bash
grep . /home/rick/second\ ingredient
```

This retrieves the first/second ingredient content.

---

## 5 — Privilege escalation

### 5.1 Search for privilege escalation vectors

Perform common checks (sudo rights, SUID binaries, misconfigurations):

```bash
sudo -l
ls -la /root
```

If `sudo -l` reveals allowed commands for the `www-data` user or other misconfigurations, use them to gain a root shell. In this exercise, a vector was found that allowed escalation to root.

### 5.2 Read root-only file (third ingredient)

With root access, read the final ingredient:

```bash
grep . /root/3rd.txt
```

Collect the final flag/ingredient — objective complete.

---

## 6 — Summary of key steps

1. **Nmap scan** → ports 22 and 80 open.
2. **Inspected web page source** → username in HTML comment (`R1ckRul3s`).
3. **robots.txt** → password candidate (`Wubbalubbadubdub`).
4. **Gobuster** → discovered `/login.php`.
5. **Authenticated** to admin panel using `R1ckRul3s:Wubbalubbadubdub`.
6. **Command execution** via admin panel → limited shell as `www-data`.
7. **Enumerated `/home/rick`** → found ingredient file (used `grep` because `cat` was blacklisted).
8. **Privilege escalation** (e.g., `sudo -l`) → obtained root.
9. **Read `/root/3rd.txt`** → final ingredient retrieved.

---

## 7 — Recommendations & defensive notes

For system administrators to prevent these common issues:

* **Remove sensitive data from served files**: never store credentials in HTML comments or `robots.txt`.
* **Harden web interfaces**: validate and sanitize inputs; avoid exposing command execution panels to authenticated users unless strictly necessary.
* **Limit credentials reuse**: use strong, unique passwords and two-factor authentication for admin accounts.
* **Harden sudoers**: avoid granting excessive or unnecessarily specific sudo privileges to web service accounts.
* **Disable dangerous functionalities** in web apps (e.g., remote command execution) unless absolutely required and audited.
* **Monitor for web-based anomalies** and scan for exposed secrets (comments, config files, backup files).

---

## 8 — Conclusion

The machine was solved by leveraging publicly exposed credentials (HTML comment + `robots.txt`) to access an admin panel providing command execution. From a limited `www-data` shell, file enumeration in `/home/rick` returned a first ingredient; a privilege escalation vector allowed reading the final root-only ingredient. All three ingredients were successfully recovered.
