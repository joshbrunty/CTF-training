# Networking & Packet Analysis — Decode the Payload

**CTF Training Series** · **Flag format:** `CTF{...}`
**Level:** Intermediate-friendly — builds on Wireshark stream-following and your Crypto toolkit (Base64, XOR).

> A short, hands-on lab. Two captures are provided. Each hides a flag that is **split across two packets** — you must read *both* packets and **decode** them to recover the flag. The flags aren't printed here on purpose. Work in your Kali VM with **Wireshark** (or `tcpdump -r`).

---

## Learning Objectives

By the end of this lab, you will be able to:

1. Locate the payload-carrying packets in a capture and read their bodies with **Follow Stream** or `tcpdump -A`.
2. Recognize **Base64** on the wire (`A–Za–z0–9`, `=` padding) and decode it.
3. Correlate **two packets** — telling a key apart from a ciphertext — and combine them.
4. Apply a **repeating-key XOR** to turn ciphertext bytes back into readable text.

---

## The Idea

Evidence is rarely sitting in one place. In both of these captures the flag has been **broken into pieces and encoded**, with the pieces sent in separate HTTP requests. Neither packet alone gives you the answer — you have to pull out *both* payloads, decode them, and put them back together. This is the bridge between Packet Analysis (find and reassemble) and Crypto (decode).

### Your workflow
1. **Open** the capture; list the packets (two HTTP POSTs each).
2. **Follow Stream** on each packet — or `tcpdump -r <file> -A` — and copy the body text.
3. **Decode** each body (Base64 first).
4. **Combine** the two results as the challenge requires, then read the `CTF{...}` flag.

---

## Files Provided

| File | Challenge | Difficulty |
|---|---|---|
| `packet_decode.pcap`  | 1 — Two Halves | Easy |
| `packet_decode2.pcap` | 2 — Handshake + Payload | Intermediate |

> Open each in Wireshark, or read it with `tcpdump -r <file> -A`, in your Kali VM.

---

### Challenge 1 — Two Halves (`packet_decode.pcap`)

The flag was cut in half. Each half was **Base64-encoded** and sent in its own HTTP POST (`/part1` and `/part2`). Decode both and join them in order.

- Read the body of each POST (**Follow → HTTP Stream**).
- Base64-decode each body. Each gives you a **piece** of the flag.
- Concatenate the two pieces — that's the flag.

> Getting started: `http.request.method == "POST"`

### Challenge 2 — Handshake + Payload (`packet_decode2.pcap`)

Two POSTs again — but this time they play different roles. One packet (`/handshake`) carries a **key**; the other (`/payload`) carries the **encrypted** flag. Figure out which is which.

- Base64-decode the `/handshake` body → you get a short, readable **key**.
- Base64-decode the `/payload` body → you get raw **cipher bytes** (not readable yet).
- **XOR** the cipher bytes against the key, repeating the key as needed, to reveal the flag.

> A single-byte XOR won't cut it here — the key is more than one character. Loop it: `key[i % len(key)]`.

---

## Submission

| Challenge | Flag |
|---|---|
| 1 — Two Halves | `CTF{________________}` |
| 2 — Handshake + Payload | `CTF{________________}` |

---

## Hints (Use Only If Stuck!)

<details>
<summary>Challenge 1 — nudge</summary>

Each POST body is Base64 (notice the `=` padding). Decode both and stick them together:
```bash
tcpdump -r packet_decode.pcap -A            # read the two bodies
echo '<part1 body>' | base64 -d ; echo
echo '<part2 body>' | base64 -d ; echo
# join the two outputs -> CTF{...}
```
</details>

<details>
<summary>Challenge 2 — nudge</summary>

The `/handshake` body Base64-decodes to a short word — that's your XOR key. The `/payload` body Base64-decodes to bytes that look like garbage until you XOR them with that key:
```python
import base64
key  = base64.b64decode('<handshake body>')
data = base64.b64decode('<payload body>')
print(bytes(b ^ key[i % len(key)] for i, b in enumerate(data)).decode())
```
</details>

---

## Common Pitfalls

- **Reading only one packet.** Both captures need *both* payloads — one packet is never the whole flag.
- **Forgetting to decode.** The bodies are Base64; decode before you try to read (or XOR).
- **Using a single-byte XOR in Challenge 2.** The key is multi-byte — repeat it across the data.
- **Wrong order.** In Challenge 1, join the halves in packet order (`/part1` then `/part2`).

---

*Prepared by Coach Josh Brunty*
*Contact: [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu) | [coachbrunty@uscybergames.org](mailto:coachbrunty@uscybergames.org)*
*CTF Training Series*
