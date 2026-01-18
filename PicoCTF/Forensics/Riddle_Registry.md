# Riddle Registry

[picoCTF](https://play.picoctf.org/practice/challenge/530)



## Write-up — *Riddle Registry* (picoCTF)

**Author:** Prince Niyonshuti N.
**Category:** Forensics

**Description:**

A mysterious PDF filled with unreadable text was discovered. Some parts are blacked out, while others appear to be simple decoys. The real treasure — the flag — is hidden elsewhere… inside the **metadata** of the file.



## PDF Analysis

After downloading the PDF file, several things are immediately noticeable:

* Multiple **blacked-out** (masked) areas
* Visually confusing and suspicious text

By copying and pasting the PDF content into a text editor (such as Notepad), we observe that:

* The masked areas are actually **decoys**
* Some parts explicitly contain messages such as:

```
nothing here nice try though
```

This confirms that the visible content of the PDF is intentionally misleading.



## Common Attempts (Decoys)

Several standard techniques were tested without success:

* `steghide` → PDF format not supported
* `strings | grep` → no meaningful output
* `file` → only confirms that the file is a valid PDF

At this point, no useful information is found in the raw content of the file.



## Re-reading the Challenge Description

After carefully re-reading the challenge description, one key phrase stands out:

> “uncover the flag within the metadata”

This strongly suggests that the flag is **not** hidden in the visible content, but in the **PDF metadata**.



## Metadata Analysis

The next step is to analyze the metadata using `exiftool`:

```bash
exiftool confidential.pdf
```

In the **Author** field, the following string is found:

```
cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV80MjQ0MGM3ZH0=
```

This format strongly resembles **Base64 encoding**.



## Decoding

After decoding the Base64 string (using CyberChef or any Base64 decoder), we obtain the flag.


## Conclusion

This challenge highlights several important lessons:

* The visible content of a file can be a **decoy**
* Always analyze **metadata**, especially when hinted in the description
* `exiftool` is an essential tool for forensics challenges
* Carefully reading the problem statement can save a significant amount of time
