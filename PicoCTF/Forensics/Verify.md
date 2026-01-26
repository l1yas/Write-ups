# picoCTF — Verify (Write-up)

**Platform:** picoCTF
**Category:** Forensics



## Challenge Analysis

The challenge provides:

* A `files/` directory containing a large number of randomly named files
* A script called `decrypt.sh` with the following usage:

```bash
./decrypt.sh <filename>
```

The goal is to identify **the only file that is actually encrypted** among many decoys.



## Script Analysis

By reading `decrypt.sh`, we observe the following command:

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -iter 100000 -salt \
  -in "/home/ctf-player/drop-in/$file_name" -k picoCTF
```

Key points:

* The script expects a **path relative to `/drop-in/`**
* The encryption key is hardcoded as `picoCTF`
* Invalid files intentionally trigger OpenSSL errors



## Automated Decryption

From inside the `drop-in` directory, we test **every file** in the `files/` directory:

```bash
for f in files/*; do
  ./decrypt.sh "$f"
done
```

Most files return the following output:

```
bad magic number
This flag is fake! Keep looking!
```



## Intelligent Filtering

To avoid manually reading all the error messages and keep only useful output, we filter the results using `grep`:

```bash
for f in files/*; do
  ./decrypt.sh "$f"
done | grep picoCTF
```



## Result

Only one file produces a valid output:

```
picoCTF{trust_but_verify_XXXXX}
```



## Conclusion

This challenge highlights:

* The importance of **reading and understanding a script before using it**
* Correct handling of **relative paths**
* Bash automation for repetitive tasks
* A fundamental security principle:
