# Challenge — Morse Code

**Category:** Cryptography
**Description:**
A WAV audio file is provided. Decode the hidden message to obtain the flag.



## Initial Analysis

The [challenge](https://play.picoctf.org/practice/challenge/280) provides a `.wav` audio file.
Audio-based challenges often involve encoded signals, and in this case the sound consists of short and long beeps separated by silence, which is characteristic of **Morse code**.



## Audio Analysis

1. Download the provided WAV file.
2. Open the file using **[wavacity.com](https://wavacity.com/)**, an online audio editor.
3. Listen to the audio and observe the waveform.
4. The pattern of short and long tones confirms that the message is encoded in Morse code.



## Decoding the Message

* The Morse code is manually transcribed from the audio.
* The transcribed Morse sequence is then decoded using **CyberChef** with the *From Morse Code* operation.

The decoded message is: (Don't forget to add an underscore where spaces should be)

```
WH47_H47H_90D_W20U9H7
```



## Flag

Since picoCTF flags follow a standard format, the decoded message is wrapped accordingly:

```
picoCTF{WH47_H47H_90D_W20U9H7}
```



## Conclusion

This challenge required:

* Identifying Morse code in an audio file
* Using an audio editor to analyze the signal
* Decoding the Morse message to retrieve the flag

A straightforward and classic example of audio cryptography in picoCTF.
