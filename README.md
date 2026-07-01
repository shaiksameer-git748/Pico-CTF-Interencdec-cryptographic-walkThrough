# Pico-CTF-Interencdec-cryptographic-walkThrough
# 🔐 Interencdec – picoCTF Writeup

## Challenge Information

- **Challenge:** Interencdec
- **Category:** Cryptography
- **Difficulty:** Easy
- **Platform:** picoCTF

---

# Challenge Description

The challenge provides the following encoded string:

```text
YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclgyMHdNakV5TnpVNGZRPT0nCg==
```

The objective is to recover the original flag.

The challenge name **Interencdec** is a clue itself:

- **Inter** → Intermediate
- **Enc** → Encoding
- **Dec** → Decoding

This suggests that the data has been encoded multiple times, and each layer must be reversed to obtain the original message.

---

# Reconnaissance

The first step was to inspect the provided string.

The ciphertext ends with:

```text
==
```

The `=` padding is a strong indicator that the string is **Base64 encoded**.

Instead of guessing, the first step was to perform a Base64 decode.

---

# Layer 1 – Base64 Decode

After decoding the original string, the output was:

```text
b'd3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX20wMjEyNzU4fQ=='
```

The leading:

```python
b''
```

is simply Python's representation of a **bytes object** and is not part of the actual data.

Removing the `b''` wrapper leaves:

```text
d3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX20wMjEyNzU4fQ==
```

---

# Layer 2 – Base64 Decode Again

The new string also ends with:

```text
==
```

which again indicates Base64 encoding.

Decoding it a second time produced:

```text
wpjvJAM{jhlzhy_k3jy9wa3k_m0212758}
```

At this point, the output was readable but clearly not a valid picoCTF flag.

---

# Layer 3 – Cipher Analysis

The text:

```text
wpjvJAM{jhlzhy_k3jy9wa3k_m0212758}
```

resembles a shifted alphabet.

Since picoCTF flags normally begin with:

```text
picoCTF{
```

the first few characters strongly suggested a **Caesar Cipher**.

Using a Caesar Cipher decoder and trying all possible shifts revealed the correct plaintext.

---

# Final Flag

```text
picoCTF{caesar_d3cr9pt3d_f0212758}
```

---

# Why This Works

This challenge combines **multiple encoding layers** with a **classical substitution cipher**.

The protection process can be visualized as:

```
Original Flag
        │
        ▼
Caesar Cipher
        │
        ▼
Base64 Encode
        │
        ▼
Base64 Encode
```

To recover the flag, the process must be reversed in the opposite order:

```
Ciphertext
        │
        ▼
Base64 Decode
        │
        ▼
Base64 Decode
        │
        ▼
Caesar Decode
        │
        ▼
Original Flag
```

---

# Methodology

```
Read Challenge
      │
      ▼
Inspect Ciphertext
      │
      ▼
Identify Base64
      │
      ▼
Decode
      │
      ▼
Still Base64?
      │
      ▼
Decode Again
      │
      ▼
Readable Text?
      │
      ▼
Identify Caesar Cipher
      │
      ▼
Brute-force Shift
      │
      ▼
Recover Flag
```

---

# Tools Used

- CyberChef
- Base64 Decoder
- Caesar Cipher Decoder
- Python (optional)

---

# Python Solution

```python
import base64

cipher = "YidkM0JxZGtwQlRYdHFhR3g2YUhsZmF6TnFlVGwzWVROclgyMHdNakV5TnpVNGZRPT0nCg=="

# First Base64 Decode
layer1 = base64.b64decode(cipher).decode()

# Remove Python bytes notation
layer1 = layer1[2:-1]

# Second Base64 Decode
layer2 = base64.b64decode(layer1).decode()

print(layer2)

# Output:
# wpjvJAM{jhlzhy_k3jy9wa3k_m0212758}

# Decode the result using a Caesar Cipher (shift 7)
# Final Flag:
# picoCTF{caesar_d3cr9pt3d_f0212758}
```

---

# Security Lessons Learned

- **Encoding is not encryption.** Base64 is only an encoding format and provides no security.
- A message can contain **multiple layers of encoding**, so decoding once may not reveal the original content.
- Always inspect the output after each decoding step before deciding the next action.
- Challenge names often provide valuable hints about the intended solution.

---

# Key Takeaways

- Never assume there is only one layer of encoding.
- Look for common encoding indicators such as `=` padding for Base64.
- If decoded output still doesn't resemble the expected format, continue analyzing it instead of stopping.
- Develop a systematic approach by identifying and reversing one transformation at a time.

---

# Skills Practiced

- Base64 Decoding
- Multi-layer Encoding Analysis
- Caesar Cipher Identification
- Classical Cryptography
- Python Scripting
- Cryptographic Reconnaissance

---

# What I Learned

This challenge demonstrated that cryptographic CTFs often combine multiple techniques rather than relying on a single encoding or cipher. By carefully analyzing each layer, identifying Base64 encoding through its padding, and recognizing the final Caesar Cipher, I was able to systematically recover the original flag. The biggest lesson from this challenge is to **approach encoded data step by step instead of assuming the first decoded output is the final answer**.

---

## Final Flag

```text
picoCTF{caesar_d3cr9pt3d_f0212758}
```
