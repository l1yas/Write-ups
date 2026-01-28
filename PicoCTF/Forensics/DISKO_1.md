# picoCTF — [DISKO 1](https://play.picoctf.org/practice/challenge/505) (Write-up)

**Author:** Darkraicg492

**Category:** Forensics



## Provided Files

* `disk.dd.gz`: compressed disk image



## Solution Steps

### 1. Download the file

```bash
wget <challenge_url>
```



### 2. Decompress the disk image

```bash
gunzip disk.dd.gz
```

This produces:

```
disk.dd
```



### 3. Identify the file type

```bash
file disk.dd
```

Output:

```
disk.dd: DOS/MBR boot sector, ...
```

This confirms that the file is a **raw disk image**, commonly used in forensic analysis.



### 4. Quick content inspection

```bash
head -n 5 disk.dd
```

The output contains structured but unreadable data, which is expected for a binary file.



### 5. Extract readable strings

In many picoCTF forensic challenges, the flag is stored **in plain text** somewhere in the disk image.

We use `strings` to extract ASCII strings and `grep` to filter for the flag format:

```bash
strings disk.dd | grep picoCTF
```



## Result

The command directly returns the flag:

```
picoCTF{1t5_ju5t_4_5tr1n9_xxxxxxx}
```



## Conclusion

This challenge highlights:

* Basic usage of **forensics tools**
* Analysis of a **raw disk image**
* The effectiveness of `strings` for detecting sensitive data left in cleartext

In this case, **no partition extraction or mounting was required**—
the flag was simply present as an ASCII string within the disk image.



## Final Flag

```
picoCTF{1t5_ju5t_4_5tr1n9_xxxxxxx}
```
