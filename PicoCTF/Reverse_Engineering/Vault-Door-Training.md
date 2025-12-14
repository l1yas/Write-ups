# **Write-up – Vault-Door-Training (picoCTF)**

## **Challenge Objective**

The [challenge](https://play.picoctf.org/practice/challenge/7) informs us that the *training vault door* is controlled by a Java program.

The mission is straightforward: **retrieve the password by carefully reading the source code**.

From the very beginning, the name *VaultDoorTraining* strongly suggests that this is an introductory exercise meant to ease the player into source-code analysis.

---

## **Step 1 — Retrieving the Source File**

We are provided with the following file:

```
VaultDoorTraining.java
```

The challenge implicitly hints that the password is embedded directly within the code.
As we shall see, this assumption proves to be entirely correct.

---

## **Step 2 — Code Review and Analysis**

At the heart of the program lies the following method:

```java
// The password is below. Is it safe to put the password in the source code?
// What if somebody stole our source code? Then they would know what our
// password is. Hmm... I will think of some ways to improve the security
// on the other doors.
//
// -Minion #9567
public boolean checkPassword(String password) {
    return password.equals("w4rm1ng_Up_w1tH_jAv4_000iPnsaWOY");
}
```

The logic could not be more direct:

* The function receives a password as input.
* It compares it **verbatim** to a hardcoded string.
* If both strings match, access is granted.

There is:

* no encoding,
* no hashing,
* no obfuscation,
* no additional logic.

In other words, the password is stored **in plain text**, making this challenge a textbook example of what *not* to do.

---

## **Step 3 — Extracting the Flag**

The password hardcoded in the source code is:

```
w4rm1ng_Up_w1tH_jAv4_000iPnsaWOY
```

picoCTF flags always follow this format:

```
picoCTF{<password>}
```

Thus, the final flag is:

```
picoCTF{w4rm1ng_Up_w1tH_jAv4_000iPnsaWOY}
```

---

## **Educational Insight**

This challenge exists to teach one fundamental lesson:

> **Never store secrets directly in source code.**

If an attacker gains access to:

* the source code,
* a Java `.class` file,
* a JAR archive,
* or any compiled Java binary (which is trivially decompilable),

then the secret is immediately compromised.

Java, in particular, offers *very weak resistance* to reverse engineering when secrets are embedded naively.
