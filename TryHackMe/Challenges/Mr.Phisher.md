# Mr.Phisher Write-up

## Introduction

In this [challenge](https://tryhackme.com/room/mrphisher), we received a suspicious email containing a strange attachment. When opened, the document repeatedly prompted us to “Enable Macros.”

The objective was to analyze the attachment safely and uncover the hidden flag.



## What Are Macros?

Macros are scripts embedded in Microsoft Office documents (Word, Excel, etc.), typically written in VBA (Visual Basic for Applications).

They are designed to automate tasks, but they are also widely abused in:

* Phishing campaigns
* Malware loaders
* Droppers
* Ransomware delivery chains

Macros are disabled by default in modern Office installations because they can execute arbitrary code. When a document asks a user to enable macros, it is often a strong indicator of malicious intent.



## Initial Investigation

After deploying the virtual machine, the relevant files were located in:

```
/home/ubuntu/mrphisher
```

Opening the document revealed:

* A message instructing the user to enable macros
* A dumb phishing meme

Instead of enabling macros (which could execute malicious code), the correct approach is static analysis.



## Extracting the VBA Code

There are two main approaches:

* Open the document
* Tools > Macros > Edit Macros
* Inspect the modules manually


## Identifying the Obfuscation

The macro contained the following logic:

```vb
a = Array(...)
b = b & Chr(a(i) Xor i)
```

This pattern immediately indicates:

* A hardcoded array of integers
* XOR operation using the loop index
* Character reconstruction using `Chr()`

This is a classic obfuscation technique.

The logic is:

For each index `i`:

```
decoded_char = a(i) XOR i
```

The resulting value is converted to a character and appended to a string.



## Reproducing the Decoding in Python

To decode the payload safely, we replicate the logic in Python:

```python
a = [102, 109, 99, 100, 127, 100, 53, 62, 105, 57, 61, 106, 62, 62, 55,
     110, 113, 114, 118, 39, 36, 118, 47, 35, 32, 125, 34, 46, 46,
     124, 43, 124, 25, 71, 26, 71, 21, 88]

decoded = ""

for i in range(len(a)):
    decoded += chr(a[i] ^ i)

print(decoded)
```



## Flag

Running the script reveals:

```
flag{a39a07a239aacd40c948d852a5c9f8d1}
```



## Why This Technique Is Used

XOR-based obfuscation is commonly used because:

* It hides readable strings from static detection
* It bypasses simple antivirus signature matching
* It is reversible and computationally cheap

A key property of XOR:

```
A XOR B XOR B = A
```

This makes it ideal for simple encoding/decoding routines.



## Security Takeaways

This challenge demonstrates a classic phishing technique:

1. The victim receives a document.
2. The document asks to enable macros.
3. The macro executes hidden code.
4. The code may download or execute a malicious payload.

In real-world attacks, this often leads to:

* PowerShell execution
* Remote Access Trojan (RAT) deployment
* Credential theft
* Ransomware infection

Users should never enable macros in documents from untrusted sources.



## Conclusion

This challenge focused on recognizing macro-based obfuscation and safely extracting hidden content without executing potentially malicious code.

The key skills demonstrated were:

* Identifying suspicious macro behavior
* Extracting VBA code safely
* Recognizing XOR obfuscation patterns
* Reconstructing hidden payloads programmatically

Understanding these techniques is essential for both red team analysis and defensive malware investigation.
