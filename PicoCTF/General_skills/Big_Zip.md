# PicoCTF – Big Zip (Write-Up)

## Challenge Context

**Platform:** [PicoCTF](https://play.picoctf.org/practice/challenge/322)
**Category:** General Skills
**Difficulty:** Easy

The **Big Zip** challenge provides a large ZIP archive containing **a huge number of nested directories and files**. The goal is to locate **a specific text file containing the flag `picoCTF{...}`** without manually browsing the entire directory tree.

## Objective

Quickly find the flag **without opening or inspecting files one by one**, by using appropriate Unix tools.


## Content Analysis

After extracting the ZIP archive, we obtain:

* A main directory
* **Dozens of subdirectories**
* Text files deeply scattered throughout the directory structure

Manual exploration would be **inefficient and unrealistic**.


## Command Used

From the root directory of the extracted archive:

```bash
grep -r 'pico' .
```

### Command Explanation

* `grep`: text search tool
* `-r`: recursive search through all subdirectories
* `'pico'`: search pattern (broader than `picoCTF` to ensure detection)
* `.`: current directory



## Result

The command returns, among other results:

```
./big-zip-files/folder_pmbymkjcya/folder_cawigcwvgv/folder_ltdayfmktr/folder_fnpfclfyee/whzxrpivpqld.txt:
information on the record will last a billion years. Genes and brains and books encode picoCTF{gr3p_15_m4g1c_XXXXXXX}
```

The flag is directly visible inside the file `whzxrpivpqld.txt`.



## Flag

```
picoCTF{gr3p_15_m4g1c_XXXXXXX}
```



## Key Takeaway

This challenge highlights a fundamental principle in CTFs (and in security in general):

> Always automate what can be automated.

Key points:

* `grep -r` allows instant exploration of complex directory structures
* Basic Unix tools are extremely powerful
* Searching broadly (`pico`) can be more effective than searching precisely (`picoCTF`)

The flag itself delivers the message clearly:

> gr3p_15_m4g1c


## Conclusion

The **Big Zip** challenge is simple but essential for learning how to:

* Avoid wasting time on manual tasks
* Master core Unix search tools
* Adopt an efficient and professional CTF mindset
