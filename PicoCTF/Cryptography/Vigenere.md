# Vigenere

[picoCTF](https://play.picoctf.org/practice/challenge/316)



## Write-up — *Vigenere* (picoCTF)

**Author:** Mubarak Mikail
**Category:** Cryptography
**Difficulty:** Easy



## Description

An encrypted message is provided.

The challenge statement explicitly gives the key to use: **`CYLAB`**.

This is a classic **Vigenère cipher**.



## Retrieving the Message

After downloading the file and displaying its contents:

```bash
cat message.txt
```

We obtain:

```
rgnoDVD{O0NU_WQ3_G1G3O3T3_A1AH3S_2951c89f}
```

The format strongly resembles a picoCTF flag, but encrypted.



## Identifying the Cipher

Several clues clearly indicate the encryption method:

* The key is explicitly provided
* The ciphertext contains uppercase letters, numbers, and underscores
* The challenge name confirms the use of the **Vigenère cipher**

There is no ambiguity: the task is simply to **decrypt the message using the given key**.



## Decryption

* Tool used: **CyberChef**
* Operation: **Vigenère Decode**
* Key: `CYLAB`

Decryption result:

```
picoCTF{D0NT_US3_V1G3N3R3_C1PH3R_2951a89h}
```



## Flag

```
picoCTF{D0NT_US3_V1G3N3R3_C1PH3R_2951a89h}
```



## Conclusion

This challenge serves as a reminder that:

* **Vigenère is a weak encryption scheme**
* If the key is known (or short), decryption is trivial
* It should **never** be used to protect sensitive data

A simple, effective, and educational challenge.
