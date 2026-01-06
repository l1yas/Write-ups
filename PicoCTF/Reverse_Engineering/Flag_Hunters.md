# Write-up – Flag Hunters | picoCTF

> A reverse engineering challenge where user input is partially reused by the server, allowing a simple logic shortcut to reach the flag.



## 1. Introduction

The **[Flag Hunters](https://play.picoctf.org/practice/challenge/472)** challenge from picoCTF exposes a remote service accessible via `nc`.

After connecting, the server repeatedly prints short poems or verses related to cybersecurity topics.
After each verse, it prompts the user with:

```text
Crowd:
```

and waits for input.



## 2. Observing the Input Behavior

Whatever is sent by the user is echoed back in the output:

```text
Crowd: <your input>
```

At first glance, the service appears to simply read input and continue printing verses, regardless of content.

However, further testing shows that **some inputs influence the program flow**.



## 3. Critical Observation – `; RETURN 1`

While most inputs merely advance the dialogue, sending the following input:

```text
; RETURN 1
```

causes the program to **immediately terminate the verse sequence and print the flag**.

This behavior is consistent and reproducible.



## 4. Why `; RETURN 1` Works

The most plausible explanation, based strictly on observed behavior, is that:

* User input is reused inside a server-side function
* The semicolon (`;`) acts as a **statement separator**
* `RETURN 1` prematurely exits the current logic block

This strongly suggests that the input is being inserted into a structured statement (or interpreted context) where `RETURN 1` is meaningful, rather than treated as a fully inert string.

In contrast to normal inputs, this specific payload alters the execution flow and triggers the flag output immediately.



## 5. Exploitation Steps

1. Connect to the service:

   ```bash
   nc <host> <port>
   ```
2. Wait for the `Crowd:` prompt
3. Send:

   ```text
   ; RETURN 1
   ```
4. The program stops iterating through verses
5. The flag is printed



## 6. Flag

```text
picoCTF{70637h3r_f0r3v3r_b248b032}
```



## 7. Conclusion

**Flag Hunters** is a simple but instructive challenge that demonstrates how:

* User input reused in program logic can introduce unintended control flow
* Even without full code execution, **early returns** can be exploited
* Minimal payloads are sometimes enough to break intended behavior

The solution does not rely on brute force, fuzzing, or iteration — only on identifying the one input that meaningfully alters execution.
