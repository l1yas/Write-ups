# **Write-up — Reverse Engineering Challenge (Transformation)**

### Link to the challenge:

[picoCTF](https://play.picoctf.org/practice/challenge/104)

---

## **Challenge Objective**

We are given a strange-looking string:

```
灩捯䍔䙻ㄶ形楴獟楮獴㌴摟潦弸彥㜰㍢㐸㙽
```

Along with the Python snippet that was used to encode it:

```python
enc = ''.join([
    chr((ord(flag[i]) << 8) + ord(flag[i + 1]))
    for i in range(0, len(flag), 2)
])
```

Our mission:

 **Recover the original flag.**

---

# **Understanding the Encoding**

The encoding process works like this:

1. It takes the flag **two characters at a time**:
   `flag[i]` and `flag[i+1]`.

2. It converts the first character into an integer:
   `ord(flag[i])`.

3. It shifts this value **8 bits to the left**:
   `ord(flag[i]) << 8`
   ➝ This places it in the “high byte”.

4. It adds the second character’s code:
   `+ ord(flag[i+1])`
   ➝ This places it in the “low byte”.

5. The result becomes a **single Unicode character** stored in `enc`.

In short:

**Two characters of the flag are merged into one Unicode character.**

---

# **Decoding Strategy**

To reverse the process and recover both characters, we simply undo the operations:

1. Get the numeric code of each encoded character:
   `code = ord(ch)`

2. Extract the high byte (first letter):
   `code >> 8`
   ➝ Right shift to recover the original high 8 bits.

3. Extract the low byte (second letter):
   `code & 0xFF`
   ➝ Use a mask to keep only the lowest 8 bits.

4. Convert the extracted values back to characters:
   `chr(...)`

This technique is known as **bit‑unpacking**.

---

# **Why `>> 8` and `& 0xFF`?**

### `>> 8`

This is the inverse of `<< 8`.
It discards the rightmost 8 bits and leaves us with the leftmost 8 bits — the first character.

### 🔸 `& 0xFF`

`0xFF` = `11111111` in binary, an 8‑bit mask.
It ensures we keep only the **low byte**, where the second character was stored.

Without the mask, we would retrieve an unwanted combination of bits.

---

# 💻 **Full Decoding Script**

```python
enc = "灩捯䍔䙻ㄶ形楴獟楮獴㌴摟潦弸彥㜰㍢㐸㙽"

flag = ""

for ch in enc:
    code = ord(ch)
    flag += chr(code >> 8)     # first letter
    flag += chr(code & 0xFF)   # second letter

print(flag)
```

---

# **Illustrative Example**

Let’s take the first character: `灩`.

```python
code = ord("灩")  # 28649
```

Extracting the two bytes:

* `code >> 8` ➝ 112 ➝ `'p'`
* `code & 0xFF` ➝ 121 ➝ `'y'`

So `灩` encodes `"py"`.

---

# **Final Result**

Running the full script reveals the complete flag:

**picoCTF{16_bits_inst34d_of_8_e703b486}**

---

# **Key Takeaways**

* `<<` shifts bits left → useful for packing values together.
* `>>` shifts bits right → retrieves the high bits.
* `& 0xFF` masks out all but the lowest 8 bits.
* This style of encoding often appears in RE / crypto challenges to hide pairs of characters in Unicode.
