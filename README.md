# 🔐 Classical Cryptography & CTF Toolkit

> A hands-on Python toolkit covering the most foundational ciphers in cryptography — built for learners, CTF beginners, and anyone preparing for cybersecurity coursework.

---

## 📁 Repository Structure

```
cryptography-toolkit/
│
├── caesar_cipher.py       # Caesar shift cipher + brute-force + frequency analysis
├── rot13.py               # ROT13 & ROT47 — self-inverse encodings
├── vigenere_cipher.py     # Vigenère cipher + IoC-based auto-crack
├── base64_ctf.py          # Base64 encoding/decoding + JWT + multi-layer CTF tools
├── xor_encryption.py      # XOR encryption + single-byte & repeating-key cracking
└── README.md
```

---

## 🧠 What This Covers

| File | Cipher | CTF Relevance |
|------|--------|---------------|
| `caesar_cipher.py` | Caesar Cipher | ⭐⭐⭐ Very common in beginner CTFs |
| `rot13.py` | ROT13 / ROT47 | ⭐⭐⭐ Flags are often ROT13-encoded |
| `vigenere_cipher.py` | Vigenère Cipher | ⭐⭐⭐ Mid-level CTF cryptography |
| `base64_ctf.py` | Base64 Encoding | ⭐⭐⭐ Appears in almost every CTF |
| `xor_encryption.py` | XOR Encryption | ⭐⭐⭐⭐⭐ Most critical cipher in all of CTF |

---

## 🚀 Quick Start

No external libraries needed. Pure Python 3.

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/cryptography-toolkit.git
cd cryptography-toolkit

# Run any file directly
python caesar_cipher.py
python rot13.py
python vigenere_cipher.py
python base64_ctf.py
python xor_encryption.py
```

---

## 📄 File Breakdown

---

### 🔡 `caesar_cipher.py` — Caesar Cipher

One of history's oldest ciphers. Each letter is shifted by `N` positions in the alphabet. Julius Caesar used `shift = 3`.

```
Plaintext : ATTACK AT DAWN
Shift     : 7
Ciphertext: HAAHR HA KHDU
```

**Key functions:**

```python
encrypt(text, shift)          # Encrypt with a shift value
decrypt(text, shift)          # Decrypt (reverse shift)
brute_force(ciphertext)       # Try all 25 shifts — scan for readable output
frequency_crack(ciphertext)   # Auto-guess shift using English letter frequency
```

**CTF tip:** When you see a substitution cipher with no key, run `brute_force()` and look for readable English. Takes under a second.

---

### 🔄 `rot13.py` — ROT13 / ROT47

ROT13 is Caesar with `shift = 13`. Because `13 + 13 = 26` (full alphabet), **applying ROT13 twice restores the original** — it's self-inverse.

```
Original : Hello, CTF!
ROT13    : Uryyb, PGS!
ROT13 x2 : Hello, CTF!  ✓
```

**Key functions:**

```python
rot13(text)          # Encode OR decode (same function — self-inverse)
rot13_builtin(text)  # Using Python's str.maketrans (fastest)
rot13_codecs(text)   # One-liner using codecs module
rot47(text)          # Extends rotation to ALL printable ASCII (digits + symbols)
detect_rot13(text)   # Heuristic: does decoding produce readable English?
```

**CTF tip:** Before trying anything complex on a mystery string, run ROT13 on it. It's surprisingly common in beginner CTFs, especially for flag hints and challenge descriptions.

---

### 🗝️ `vigenere_cipher.py` — Vigenère Cipher

Polyalphabetic cipher: uses a keyword to apply *different* Caesar shifts to each letter. Far stronger than Caesar, but still crackable via **Index of Coincidence (IoC)** and **frequency analysis**.

```
Plaintext  : ATTACKATDAWN
Key        : LEMON (repeating)
Ciphertext : LXFOPVEFRNHR
```

**Key functions:**

```python
encrypt(text, key)                  # Encrypt with keyword
decrypt(text, key)                  # Decrypt with keyword
index_of_coincidence(text)          # English ≈ 0.065 | Random ≈ 0.038
guess_key_length(ciphertext)        # Kasiski/IoC method to find key length
crack_vigenere(ciphertext, key_len) # Recover key via per-stream frequency analysis
full_crack(ciphertext)              # 🔥 Fully automatic: key length + key + plaintext
```

**CTF tip:** If you suspect Vigenère, run `full_crack()` directly. It combines IoC key-length guessing with frequency analysis and returns the plaintext with no human steps needed.

---

### 📦 `base64_ctf.py` — Base64

Base64 is an **encoding scheme, not encryption**. It converts binary → printable ASCII using 64 characters (`A–Z a–z 0–9 + /`). Anyone can decode it — no key needed.

```
"flag{hello}"  →  "ZmxhZ3toZWxsb30="
```

**Key functions:**

```python
b64_encode(text)              # Standard Base64 encode
b64_decode(encoded)           # Standard Base64 decode
b64_url_encode(text)          # URL-safe variant (used in JWTs)
b64_url_decode(encoded)       # URL-safe decode (auto-fixes missing padding)
is_base64(s)                  # Detect if a string looks like Base64
decode_layers(encoded)        # 🔥 Peel multiple encoding layers (Base64 of Base64...)
b64_to_hex(encoded)           # Base64 → hex string
hex_to_b64(hex_str)           # Hex → Base64
decode_jwt_payload(token)     # Extract JWT payload without knowing the secret
```

**CTF tip:** Always try `decode_layers()` — CTFs frequently encode the flag 2–4 times. Also, if you see a JWT in a web challenge, `decode_jwt_payload()` reveals the payload without needing the signing secret.

---

### ⚡ `xor_encryption.py` — XOR Encryption

**The most important cipher in CTF cryptography.** XOR (`^`) is the building block of stream ciphers, OTP, and almost every custom "encryption" you'll encounter in CTFs.

```
Core property:
  plaintext  ^ key = ciphertext
  ciphertext ^ key = plaintext   ← same operation!
```

**Key functions:**

```python
xor_bytes(data, key)                    # XOR bytes with repeating key (core function)
xor_encrypt(plaintext, key)             # Encrypt string → cipher bytes
xor_decrypt(cipher_bytes, key)          # Decrypt cipher bytes → string
xor_hex(hex1, hex2)                     # XOR two hex strings (common CTF format)
brute_single_byte_xor(cipher_bytes)     # 🔥 Try all 256 key bytes, rank by English score
detect_xor_key_length(cipher_bytes)     # Hamming distance to guess repeating key length
crack_repeating_key_xor(cipher, klen)   # 🔥 Cryptopals-style full key recovery
known_plaintext_attack(cipher, known)   # 🔥 If "flag{" is known, recover key prefix
```

**CTF tip — the three most common XOR scenarios:**

1. **Single-byte key** → `brute_single_byte_xor()` cracks it instantly
2. **Repeating key, unknown length** → `detect_xor_key_length()` then `crack_repeating_key_xor()`
3. **You know the flag format starts with `flag{`** → `known_plaintext_attack()` leaks the key

---

## 🧩 Key Concepts

### Why XOR is Everywhere

```python
# XOR is its own inverse — elegant and fast
A ^ B = C
C ^ B = A   # same as the first operation
```

Modern ciphers like AES use XOR internally. Stream ciphers are essentially XOR with a pseudorandom keystream. Understanding XOR unlocks a huge portion of CTF crypto challenges.

### Base64 ≠ Encryption

A common misconception. Base64 is just a way to represent binary as text. There is no key and no security. If you see Base64, decode it immediately — it's information waiting to be read.

### Index of Coincidence (IoC)

A statistical measure of how unevenly letters are distributed:

| Text Type | IoC Value |
|-----------|-----------|
| English plaintext | ~0.065 |
| Random / encrypted | ~0.038 |
| Caesar cipher | ~0.065 (same as English — same distribution, just shifted) |
| Vigenère cipher | between 0.038 and 0.065 (depends on key length) |

Used to distinguish plaintext from ciphertext and to guess Vigenère key lengths.

### Frequency Analysis

In English, letters appear at predictable rates. The most common: **E T A O I N S H R D L U**

Caesar and single-byte XOR are both vulnerable because they shift every letter by the same amount, preserving the frequency distribution. Finding the most common letter in ciphertext and assuming it maps to `E` is often enough to crack the key.

---

## 🏁 CTF Cheat Sheet

| Scenario | Tool to use |
|----------|-------------|
| Mystery text, looks like shifted letters | `caesar_cipher.py → brute_force()` |
| String that looks garbled but printable | `rot13.py → rot13()` |
| Ends in `=` or `==`, only A-Za-z0-9+/ | `base64_ctf.py → decode_layers()` |
| Polyalphabetic cipher, keyword-based | `vigenere_cipher.py → full_crack()` |
| Hex blob, encrypted with unknown byte | `xor_encryption.py → brute_single_byte_xor()` |
| Flag format known (`flag{`), XOR encrypted | `xor_encryption.py → known_plaintext_attack()` |
| JWT token in a web challenge | `base64_ctf.py → decode_jwt_payload()` |
| Two hex strings given, "find what they produce" | `xor_encryption.py → xor_hex()` |

---

## 📚 Learning Resources

- [PicoCTF](https://picoctf.org/) — beginner-friendly CTF with great crypto challenges
- [TryHackMe](https://tryhackme.com/) — guided cybersecurity learning paths
- [CryptoHack](https://cryptohack.org/) — dedicated cryptography CTF platform
- [Cryptopals Challenges](https://cryptopals.com/) — the gold standard for learning crypto by breaking it
- [Hack The Box](https://www.hackthebox.com/) — intermediate+ CTF platform

---

## 🎯 Roadmap / What's Next

- [ ] Substitution cipher solver (frequency mapping)
- [ ] Rail fence & columnar transposition ciphers
- [ ] Hill cipher (matrix-based)
- [ ] RSA basics (modular arithmetic, small `e` attacks)
- [ ] AES-ECB penguin attack demo
- [ ] Hash cracking basics (MD5, SHA-1 with wordlists)

---

## 👤 Author

Built as part of preparation for advanced cybersecurity coursework and CTF competitions.  
All implementations are written from scratch in pure Python for learning purposes.

---

## ⚠️ Disclaimer

This toolkit is for **educational purposes only**. All techniques demonstrated here are standard curriculum in cryptography courses and CTF competitions. Use responsibly.

---

*"Cryptography is the art of writing and solving codes. Understanding classical ciphers is the foundation on which all of modern cryptosecurity is built."*
