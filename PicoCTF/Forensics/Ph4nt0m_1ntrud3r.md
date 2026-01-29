# Ph4nt0m 1ntrud3r — Write-up

**Author:** Prince Niyonshuti N.
**Category:** Forensics / Network
**Provided file:** PCAP

Challenge link:
[https://play.picoctf.org/practice/challenge/459](https://play.picoctf.org/practice/challenge/459)


## Initial Analysis

The provided PCAP file is opened using **Wireshark** to analyze the captured network traffic.

While inspecting the packets, several **suspicious payloads** can be observed. Some of these payloads contain strings ending with `==`, which is a common pattern associated with **Base64 encoding**.

This observation strongly suggests a potential **data exfiltration attempt**, where information is being transmitted in an encoded form to evade casual inspection.



## Extracting Suspicious Data

Instead of manually inspecting each packet in Wireshark, the analysis can be simplified by extracting readable strings directly from the PCAP file.

The following command is used:

```bash
strings file.pcap | grep '=='
```

This command extracts printable strings from the capture file and filters those that match the Base64 padding pattern.

The output reveals several Base64-encoded fragments:

```
ezF0X3c0cw==
MzE4ZGIyMg==
fQ==
XzM0c3lfdA==
cGljb0NURg==
YmhfNHJfZg==
bnRfdGg0dA==
```



## Base64 Decoding

Each extracted fragment is then decoded using a simple Python script:

```python
import base64

words = [
    "ezF0X3c0cw==",
    "MzE4ZGIyMg==",
    "fQ==",
    "XzM0c3lfdA==",
    "cGljb0NURg==",
    "YmhfNHJfZg==",
    "bnRfdGg0dA=="
]

for word in words:
    print(base64.b64decode(word).decode())
```



## Decoded Output

The decoded fragments produce the following output:

```
{1t_w4s
318db22
}
_34sy_t
picoCTF
bh_4r_f
nt_th4t
```



## Flag Reconstruction

By rearranging the decoded fragments in the correct logical order, the complete flag can be reconstructed:

```
picoCTF{1t_w4snt_th4t_34sy_tbh_4r_f318db22}
```


## Key Takeaways

* Data exfiltration can occur slowly and in encoded form to avoid detection
* Repeated Base64 padding (`==`) in network traffic is a strong indicator of encoded data
* Combining `strings` and `grep` is an effective and efficient technique for quick network forensic triage
