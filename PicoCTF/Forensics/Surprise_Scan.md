# Write-up — *Scan Surprise* (picoCTF)

### Challenge Description

The **[Scan Surprise](https://play.picoctf.org/practice/challenge/444)** challenge provides a file containing an image that appears to be a QR code.
The goal is to retrieve the hidden **flag** from this file.

The challenge does not involve any network interaction or exploitation; it focuses on basic analysis and correct tool usage.

### Analysis

After downloading the provided file, it is immediately identifiable as a **standard QR code image**.

Given the challenge category and name, the most reasonable assumption is that the QR code directly encodes useful information, most likely the flag itself.

There is no visible obfuscation:

* The QR code is well-formed
* No distortion or visual trick is applied
* No misleading metadata is present

### Exploitation

To solve the challenge, the QR code simply needs to be scanned using any QR code reader.

Possible methods include:

* Using a smartphone QR scanner
* [Online QR decoding tools](https://qrscanner.net)
* Command-line utilities such as `zbarimg`

Example using the command line:

```bash
zbarimg flag.png
```

### Result

Decoding the QR code reveals the **picoCTF flag directly**, with no additional steps required.

The challenge is solved at this stage.

---

### Conclusion

This challenge emphasizes the importance of:

* Starting with the simplest checks
* Recognizing common file formats
* Choosing the appropriate tool instead of overthinking the problem

In CTFs, not every challenge requires deep exploitation—sometimes the solution is exactly what it looks like.
