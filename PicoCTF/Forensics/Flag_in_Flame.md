
# Write-Up — *Flag in Flames* (Forensics)

## Challenge Overview

**Category:** Forensics
**[Challenge](https://play.picoctf.org/practice/challenge/523) Name:** Flag in Flames

The challenge provides a file named `logs.txt`. At first glance, it appears to be a standard log file, but the goal is to analyze its contents to uncover a hidden flag.

This challenge focuses on **data encoding, file carving, and multi-layer forensic analysis**, all common techniques in real-world forensic investigations and CTFs.



## Initial Inspection

The first step is to inspect the contents of the file:

```bash
cat logs.txt
```

While reviewing the file, a **long string composed exclusively of characters from the Base64 character set** stands out:

```
A–Z a–z 0–9 + / =
```

This strongly suggests that the data has been **Base64-encoded**, likely to conceal binary data inside a text-based log file.



## Extracting the Base64 Payload

Since the file may contain additional noise or unrelated log entries, we extract only valid Base64 characters using a regular expression:

```bash
grep -oE '[A-Za-z0-9+/=]+' logs.txt > extracted.b64
```

### Why this works:

* `grep -o` outputs only the matching part of each line
* The regex matches all valid Base64 characters
* The result is a clean Base64 payload ready for decoding



## Base64 Decoding

Next, we decode the extracted Base64 data:

```bash
base64 -d extracted.b64 > flag.png
```

At this point, we verify the file type:

```bash
file flag.png
```

The output confirms that the decoded data is a **PNG image**.

This demonstrates a common forensic technique:

> **Embedding binary files (images) inside text logs via Base64 encoding.**



## 🖼️ Image Analysis

Opening the image reveals something interesting:
Instead of a traditional picture, the image contains **text displayed visually**, specifically a long hexadecimal string:

```
7069636F43544678666F72656E736963735F616E616C797369735F69735F616D617A696E675F61633165333538347D
```

This indicates **another layer of encoding**, this time using hexadecimal representation.



## Hexadecimal Decoding

The hex string is then decoded into ASCII using CyberChef or any hex-to-text decoder:

### Hex input:

```
7069636F43544678666F72656E736963735F616E616C797369735F69735F616D617A696E675F61633165333538347D
```

### Decoded ASCII output:

```
picoCTFxforensics_analysis_is_amazing_ac1e3584}
```



## Final Observation and Correction

At this stage, the decoded string is almost a valid picoCTF flag. However, there is a small anomaly:

* The character `x` appears where `{` is expected.

This is a **deliberate obfuscation trick**, likely added to prevent automated flag extraction.

After correcting it:

```
picoCTF{forensics_analysis_is_amazing_ac1e3584}
```



## 🏁 Flag

```
picoCTF{forensics_analysis_is_amazing_ac1e3584}
```



## Lessons Learned

This challenge highlights several important forensic concepts:

* **Data hiding in logs** using Base64 encoding
* **Multi-layer encoding** (Base64 → PNG → Hex → ASCII)
* Using command-line tools for efficient forensic extraction
* Recognizing file signatures and non-text payloads
* Identifying and correcting intentional obfuscation

It reinforces the idea that **logs are not always just text** and can be abused to transport or conceal binary data.



## Conclusion

*Flag in Flames* is a well-designed forensic challenge that encourages methodical analysis and attention to detail.
By peeling back each encoding layer step by step, the hidden flag can be reliably recovered.

A solid exercise in **forensic thinking, patience, and encoding awareness**.



