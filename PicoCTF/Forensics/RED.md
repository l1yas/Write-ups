# picoCTF — RED (Write-up)

**Platform:** [picoCTF](https://play.picoctf.org/practice/challenge/460)
**Category:** Forensics / Steganography
**Provided file:** `red.png`



## Initial Analysis

The provided file is a **128×128 PNG image** that appears completely red to the naked eye.

Quick verification:

```bash
file red.png
```

Result: valid PNG image.

Metadata analysis with `exiftool`:

```bash
exiftool red.png
```

An unusual **Poem** field appears in the metadata, containing a poem focused on the color red.



## Hidden Clue in the Poem

By taking the **first letter of each line** of the poem (an acrostic), we obtain:

```
C H E C K L S B
```

Which clearly spells:

```
CHECK LSB
```

This is the key hint of the challenge.



## Interpreting the Hint

* **LSB** stands for *Least Significant Bit*
* The challenge name (**RED**) strongly suggests that:

  * The hidden data is stored in the **red color channel**
  * More specifically, in the **LSB of the red channel**



## LSB Data Extraction

By analyzing the **LSB of the red channel** (using tools such as `zsteg`, `stegsolve`, or a custom script), we extract the following encoded string:

```
cGljb0NURntyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==
```

This string appears multiple times due to the embedding method, but **a single occurrence is sufficient**.



## Decoding

The extracted string is encoded in **Base64**.

After decoding, we obtain:

```
picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}
```



## Flag

```
picoCTF{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}
```



## Conclusion

This challenge demonstrates that:

* A visually empty image can hide meaningful data
* Metadata can provide **hints**, not necessarily the solution itself
* **LSB steganography** is a classic yet highly effective technique
* Paying attention to the challenge name and textual clues is essential

An excellent exercise to reinforce understanding of image formats and low-level steganography techniques.


