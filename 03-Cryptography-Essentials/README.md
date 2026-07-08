# Cryptography Essentials — Hashing, Encoding, and Classic Ciphers

**CTF Training Series**
**Level:** Intermediate-friendly — this is where the encoding-vs-encryption idea from the intro and the `tr`/`base64`/`xxd` tools from the Linux session pay off.

> Your reference for this session. The hands-on challenges are meant to be solved yourself — answers aren't printed here on purpose. You'll want CyberChef open and a terminal handy.

---

## Learning Objectives

By the end of this session, you will be able to:

1. Tell **encoding**, **hashing**, and **encryption** apart — and know which one you're looking at.
2. Recognize and decode common encodings on sight: Base64, Base32, hex, URL/percent, decimal, and binary.
3. Identify a hash by its length/shape and crack weak ones with a wordlist.
4. Break classic ciphers: Caesar/ROT, substitution, Vigenère, and single-byte XOR.
5. Drive **CyberChef** and the command-line crypto toolkit confidently.
6. Apply a repeatable crypto workflow: **identify the scheme → find the key → decode or crack → recover the flag.**

---

## What "Crypto" Means in a CTF

Most CTF "crypto" is **not** about breaking modern encryption — that's math research, not a two-hour challenge. In practice, crypto challenges reward three things:

1. **Recognition** — knowing *what scheme* you're staring at from its tells.
2. **Tooling** — pointing the right tool (CyberChef, a hash cracker, a script) at it.
3. **Key-hunting** — most solvable challenges hand you the key, hide it nearby, or use a key small enough to brute-force.

The big mental model, carried from the intro session: **encoding ≠ hashing ≠ encryption.**

| Type | Reversible? | Needs a key? | Example |
|---|---|---|---|
| **Encoding** | Yes, trivially | No | Base64, hex, ROT13 |
| **Hashing** | No (one-way) | No | MD5, SHA-256 |
| **Encryption** | Yes, *with the key* | Yes | AES, XOR, Vigenère |

---

## Encoding — Reversible, No Secret

Encoding just re-represents data. If you can recognize it, you can reverse it.

| Encoding | How to recognize it | Reverse it with |
|---|---|---|
| **Base64** | `A–Z a–z 0–9 + /`, length a multiple of 4, often `=` padding | CyberChef *From Base64* · `base64 -d` |
| **Base32** | **Uppercase** `A–Z` and digits `2–7`, often `=` padding | CyberChef *From Base32* |
| **Hex** | Only `0–9 a–f`, even length | CyberChef *From Hex* · `xxd -r -p` |
| **URL / percent** | `%20`, `%2F` sequences | CyberChef *URL Decode* |
| **Decimal / ASCII** | space- or comma-separated numbers 0–255 | CyberChef *From Decimal* |
| **Binary** | long runs of `0` and `1` in groups of 8 | CyberChef *From Binary* |

> **Layers are normal.** Decode once, look at the result, decode again. If the output of a Base64 decode is all `0–9a–f`, you've got hex underneath. CyberChef's **Magic** wand auto-detects layers — use it to check your reasoning, not to replace it.

---

## Hashing — One-Way Fingerprints

A hash turns any input into a fixed-length fingerprint. You **cannot** reverse it — but you *can* guess inputs, hash them, and compare. That's "cracking."

| Hash | Length (hex chars) | Looks like |
|---|---|---|
| **MD5** | 32 | `0d107d09f5bbe40cade3de5c71e9e9b7` |
| **SHA-1** | 40 | 40 hex characters |
| **SHA-256** | 64 | 64 hex characters |

**Workflow for a hash challenge:**
1. **Identify** it by length (and tooling): `hashid` or `hash-identifier`.
2. **Crack** it — for weak/common inputs:
   - Online lookups (e.g. CrackStation) for very common hashes.
   - `john` or `hashcat` with the `rockyou.txt` wordlist for offline cracking.
3. **Salts** defeat lookups: a salted hash must be cracked, not looked up. Real CTFs lean on *weak* or *unsalted* hashes — that's the point.

#### First, set up the `rockyou` wordlist (one-time)

On Kali, `rockyou.txt` ships **compressed** as `rockyou.txt.gz` — you must decompress it once before John can use it. (If you point John at the `.gz`, it reads the compressed bytes as garbage and cracks nothing — you'll see a `UTF-16 BOM` warning and `0g`.)

**Easiest way — decompress it in place.** This needs `sudo`, because `/usr/share/wordlists/` is a system folder owned by root:
```bash
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
```
That swaps the `.gz` for a plain `rockyou.txt` at the standard path, and you only ever do it once. Confirm it worked:
```bash
ls -lh /usr/share/wordlists/rockyou.txt      # ~136 MB, ~14 million passwords
```

> **Got `Permission denied`, or don't want to use `sudo`?** That's the error you hit if you drop the `sudo`. Instead, decompress a **copy into your home folder** — no root needed, and it leaves the original untouched:
> ```bash
> zcat /usr/share/wordlists/rockyou.txt.gz > ~/rockyou.txt
> ```
> Then just use `~/rockyou.txt` anywhere a command says `/usr/share/wordlists/rockyou.txt`. (This is what to do if `sudo gunzip` won't cooperate — no need to move files to your Desktop.)

#### Now identify and crack

```bash
# identify
hashid 0d107d09f5bbe40cade3de5c71e9e9b7

# crack with John + rockyou
echo '0d107d09f5bbe40cade3de5c71e9e9b7' > hash.txt
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# re-display the cracked password any time (reads John's saved results):
john --show --format=raw-md5 hash.txt
```

> **Reading John's output** — the cracked password prints **inline the moment it's found**: a highlighted word followed by `(?)` (the `?` just means "no username," which is normal for a bare hash). In the summary line, `1g` = *one hash cracked*, `0g` = none. The `Warning: no OpenMP support ... consider --fork=2` line is a **speed note, not an error** — you can ignore it. If you scrolled past the result, `john --show` above prints it again.

---

## Classic Ciphers

These are encryption — reversible **with a key** — but the keys are weak enough to break.

- **Caesar / ROT-n** — every letter shifted by a fixed amount. Only 25 possibilities: brute-force all of them. ROT13 is just *n = 13*.
- **Atbash** — the alphabet reversed (A↔Z, B↔Y). No key at all.
- **Vigenère** — a Caesar shift that changes per letter according to a **keyword**. If you have the keyword, it's instant; if not, the repeating key length can be recovered statistically.
- **Substitution** — each letter maps to another consistently. Break it with **frequency analysis**: `e`, `t`, `a` are the most common English letters; short words and letter patterns give the rest.
- **Rail fence / transposition** — the letters are all there, just reordered.

> **Tell:** if the ciphertext is all letters and the letter *frequencies* look English-ish, think substitution or Vigenère. If shifting the whole thing by some `n` suddenly spells words, it was Caesar.

---

## XOR — The CTF Workhorse

XOR is reversible: `data ⊕ key ⊕ key = data`. Two patterns dominate:

- **Single-byte XOR** — the whole message is XOR'd with one byte. Only **256** keys exist — brute-force all of them and look for readable text (or for `CTF{`).
- **Repeating-key XOR** — a short key repeats across the message. Recover the key length, then solve each position like a single-byte XOR.

```python
# brute-force single-byte XOR over a hex string, looking for the flag
data = bytes.fromhex("....")
for k in range(256):
    out = bytes(b ^ k for b in data)
    if b"CTF{" in out:
        print(k, out)
```

> **Key-in-the-binary pattern:** in reverse-engineering challenges, the XOR key is often sitting right there in the code or the disassembly. Crypto and RE overlap constantly.

---

## The Crypto Workflow

1. **Identify** the scheme from its tells (charset, length, padding, letter frequencies).
2. **Find the key** — is it given? nearby? small enough to brute-force?
3. **Decode or crack** with the right tool.
4. **Recover the flag** and submit it exactly as `CTF{...}`.
5. **Note it** — which tell gave the scheme away? That recognition is the skill you're building.

---

## Tooling

- **[CyberChef](https://gchq.github.io/CyberChef/)** — the everything-tool. Stack operations into a *recipe*; use **Magic** to detect encodings.
- **Command line** — `base64 -d`, `xxd -r -p`, `tr` (ROT and cleanup), `openssl` for hashing.
- **Hash ID & cracking** — `hashid` / `hash-identifier`, then `john` or `hashcat` with `rockyou.txt`.
- **[dcode.fr](https://www.dcode.fr/en)** — excellent for classic ciphers (auto-solvers for Caesar, Vigenère, substitution).
- **Python** — a five-line script beats clicking for XOR brute force and custom logic.

---

## Hands-on Challenges 🚩

Each recovers a flag in the form `CTF{...}` (challenge 2 recovers a single word). Use CyberChef or your terminal. Identify the scheme first — that's the whole game.

**Challenge 1 — Encoding**

```
INKEM63CGRZTGMS7NA2HGX3QGRSGIMLOM56Q====
```
Look at the character set and the padding. Which *base* uses only uppercase letters and the digits 2–7?

**Challenge 2 — Hash**

```
0d107d09f5bbe40cade3de5c71e9e9b7
```
Identify the hash type by its length, then crack it. The recovered word is your flag. (Hint: it's a famously common password — a wordlist or lookup will get it fast.)

**Challenge 3 — Classic cipher**

```
JAM{y0a4a3_i3f0uk_13}
```
The flag wrapper is there but shifted. Try every rotation until `CTF{` appears — only the letters move.

**Challenge 4 — XOR**

```
190e1c21226a28056b290528692c6928296b38366927
```
This is hex, single-byte XOR'd. There are only 256 possible keys — brute-force them and watch for `CTF{`.

> Submit each flag exactly as recovered, wrapper and all.

---

## Common Tells — Quick Reference

- All `A–Za–z0–9+/` with `=` padding → **Base64**
- Uppercase + digits `2–7` → **Base32**
- Only `0–9a–f`, even length → **hex**
- 32 / 40 / 64 hex chars with no obvious meaning → **MD5 / SHA-1 / SHA-256**
- All letters, English-looking frequencies → **substitution / Vigenère**
- Shifting by some `n` spells words → **Caesar**
- Given a hex blob and a hint about a "key" → **XOR**

---

## Wrap-up & What's Next

**Recap:**
1. Encoding, hashing, and encryption are three different things — identify which before you act.
2. Recognition + the right tool solves most challenges; CyberChef and a hash cracker cover a lot of ground.
3. Weak keys are meant to be broken — brute-force the small ones, look for keys hiding nearby.

**Next session (Web Exploitation Basics):** how web apps break — SQL injection, cross-site scripting, and broken authentication.

---

## Before the Next Session

1. **Practice crypto challenges** on a platform:
   - [picoCTF](https://picoctf.org) — a deep, beginner-friendly Cryptography category.
   - [CryptoHack](https://cryptohack.org) — focused, gamified crypto training.
2. **Get comfortable in CyberChef** — rebuild today's challenges from scratch as recipes.
3. **Stretch:** crack a few MD5 hashes of common passwords with `john` and `rockyou.txt`.

---

## Resources

- **[CyberChef](https://gchq.github.io/CyberChef/)** — encoding, hashing, and crypto playground.
- **[dcode.fr](https://www.dcode.fr/en)** — classic-cipher identifiers and auto-solvers.
- **[CrackStation](https://crackstation.net)** — lookup for common unsalted hashes.
- **[CryptoHack](https://cryptohack.org)** — modern crypto, taught as challenges.
- **[picoCTF](https://picoctf.org)** — practice challenges with a Cryptography track.

---

*CTF Training Series*

