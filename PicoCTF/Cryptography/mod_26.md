# Mod 26

[picoCTF](https://play.picoctf.org/practice/challenge/144)


## Write-up — *Mod 26* (picoCTF)

**Author:** Pandu
**Category:** Cryptography


## Description

> Cryptography can be easy, do you know what ROT13 is?

A file named `values.txt` is provided.


## Analysis

We begin by displaying the contents of the file:

```bash
cat values.txt
```

Output:

```
cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_45559noq}
```

At first glance:

* The format strongly resembles a picoCTF flag
* The text appears to be encrypted using a **simple substitution cipher**
* The challenge description explicitly mentions **ROT13**


## Exploitation

ROT13 is a **modulo 26** substitution cipher where each letter is shifted by 13 positions in the alphabet.

Applying ROT13 to the ciphertext reveals the plaintext. This can be done using tools such as **CyberChef**:

* Operation: `ROT13`
* Input: the string from the file


## Result

After applying ROT13, the decoded message is the flag

## Key Takeaways

* When a challenge mentions **Mod 26**, think of alphabet-based substitution ciphers
* **ROT13 is symmetric**: applying ROT13 twice returns the original text
* Recognizing common patterns (`cvpb` → `pico`) can immediately reveal the correct approach

Dis-moi.
