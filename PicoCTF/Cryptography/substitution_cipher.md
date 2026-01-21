# picoCTF — Substitution Cipher (Write-up)

## Challenge Overview

**Challenge:** Substitution Cipher
**Platform:** picoCTF
**Category:** Cryptography

The challenge provides a ciphertext along with a custom **substitution alphabet**:

```
ZGSOCXPQUYHMILERVTBWNAFJDK
```

The goal is to decrypt the message and recover the hidden flag.



## Analysis

Several clues indicate that this is a **monoalphabetic substitution cipher**:

* The provided alphabet contains **26 unique letters**, covering the full English alphabet.
* The ciphertext strongly resembles the standard picoCTF flag format: `picoCTF{...}`.
* Only **uppercase letters are encrypted**, while lowercase letters, digits, and symbols remain unchanged.

This means we can directly map the given cipher alphabet to the standard alphabet and apply the substitution.



## Substitution Principle

We map each character from the **cipher alphabet** to the corresponding character in the **standard alphabet**:

```
Z → A
G → B
S → C
O → D
...
```

In other words:

* The first letter of the cipher alphabet maps to `A`
* The second maps to `B`
* And so on until `Z`

Once this mapping is established, we simply replace each encrypted character accordingly.



## Python Script Used

```python
cipher_alphabet = "ZGSOCXPQUYHMILERVTBWNAFJDK"
plain_alphabet  = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"

# Build substitution table
sub_map = {c: p for c, p in zip(cipher_alphabet, plain_alphabet)}

cipher_text = "picoSWX{5NG5717N710L_3A0MN710L_357GX9XX}"

decoded = ""
for c in cipher_text:
    decoded += sub_map.get(c, c)  # keep digits and symbols unchanged

print(decoded)
```



## Output

```
picoCTF{5UB5717U710N_3V0LU710N_######}
```


## Key Takeaways

* When a **custom alphabet** is explicitly provided, always consider a **simple substitution cipher**.
* Recognizing common CTF patterns like `picoCTF{}` can significantly speed up analysis.
* Automating decryption with a short script is safer and faster than manual substitution.


