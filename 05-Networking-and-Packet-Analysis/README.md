# Networking & Packet Analysis — Reading & Decoding the Wire

**CTF Training Series** · **Flag format:** `CTF{...}`
**Level:** Intermediate-friendly — builds on Wireshark stream-following and your Crypto toolkit (Base64, XOR).

> A hands-on lab with **five captures** in two tracks. **Track A** is classic packet analysis — find the conversation and read it. **Track B** adds a decode step — the flag is split across two packets and encoded, so you must reassemble *and* decode. The flags aren't printed here on purpose. Work in your Kali VM with **Wireshark** (or `tcpdump -r`).

---

## Learning Objectives

By the end of this lab, you will be able to:

1. Navigate a capture with **display filters** and **Follow Stream**.
2. Extract **credentials** and **files** from cleartext traffic (HTTP, etc.).
3. Spot a **covert channel** (data smuggled through ICMP) and reassemble it.
4. Recognize **Base64** on the wire and decode it.
5. Correlate **two packets** — telling a key from a ciphertext — and combine them with **repeating-key XOR**.

---

## The Workflow

1. **Open** the capture; skim **Statistics → Protocol Hierarchy** to see what's there.
2. **Filter** to the interesting protocol or host — cut the noise.
3. **Follow** the stream (or **Export Objects**) to read the conversation / pull a file.
4. **Decode** if needed (Base64/hex/XOR — your Crypto toolkit).
5. **Recover** the `CTF{...}` flag.

> Handy display filters: `http`, `dns`, `icmp`, `ip.addr == 10.10.10.5`, `tcp.port == 21`, `http.request.method == "POST"`.

---

## Files Provided

| File | Challenge | Track | Difficulty |
|---|---|---|---|
| [`creds.pcap`](https://github.com/joshbrunty/CTF-training/raw/main/05-Networking-and-Packet-Analysis/creds.pcap) | 1 — Cleartext Credentials | A — Analysis | Easy–Intermediate |
| [`download.pcap`](https://github.com/joshbrunty/CTF-training/raw/main/05-Networking-and-Packet-Analysis/download.pcap) | 2 — File Recovery | A — Analysis | Intermediate |
| [`icmp.pcap`](https://github.com/joshbrunty/CTF-training/raw/main/05-Networking-and-Packet-Analysis/icmp.pcap) | 3 — Covert Channel | A — Analysis | Intermediate–Hard |
| `packet_decode.pcap` | 4 — Two Halves | B — Decode | Easy |
| `packet_decode2.pcap` | 5 — Handshake + Payload | B — Decode | Intermediate |

> Open each in Wireshark, or read it with `tcpdump -r <file> -A`, in your Kali VM.

---

# Track A — Read the Conversation

### Challenge 1 — Cleartext Credentials (`creds.pcap`)

Someone logged into an internal web app over plain **HTTP** — and HTTP hides nothing.

- Open the capture and look at the **HTTP** traffic. What kind of request carries a login?
- **Follow the stream** and read the request body. The password is the flag.

> Filter to get started: `http.request.method == "POST"`

### Challenge 2 — File Recovery (`download.pcap`)

A file was downloaded over HTTP. Pull it back out of the traffic and read it.

- Find the HTTP request — what file was fetched?
- Use Wireshark's **File → Export Objects → HTTP** to save the transferred file, then open it. (Or follow the HTTP stream and read the response body.)

### Challenge 3 — Covert Channel (`icmp.pcap`)

Most of this capture is ordinary **ICMP** (ping) traffic — but someone is using ping to smuggle data out. Normal pings carry filler; these don't.

- Filter to `icmp` and look at the **payloads** of the echo requests. Some carry filler; some carry something readable.
- The smuggled data is **chunked across several packets**, in order. Collect the readable payloads and put them back together.

> A ping's data field isn't supposed to be a secret. Read it.

---

# Track B — Decode the Payload

In both of these the flag has been **broken into pieces and encoded**, with the pieces sent in separate HTTP POSTs. Neither packet alone gives the answer — pull out *both* bodies, decode them, and combine.

### Challenge 4 — Two Halves (`packet_decode.pcap`)

The flag was cut in half. Each half was **Base64-encoded** and sent in its own POST (`/part1` and `/part2`). Decode both and join them in order.

- Read the body of each POST (**Follow → HTTP Stream**).
- Base64-decode each body. Each gives you a **piece** of the flag.
- Concatenate the two pieces — that's the flag.

> Getting started: `http.request.method == "POST"`

### Challenge 5 — Handshake + Payload (`packet_decode2.pcap`)

Two POSTs again — but they play different roles. One packet (`/handshake`) carries a **key**; the other (`/payload`) carries the **encrypted** flag. Figure out which is which.

- Base64-decode the `/handshake` body → a short, readable **key**.
- Base64-decode the `/payload` body → raw **cipher bytes** (not readable yet).
- **XOR** the cipher bytes against the key, repeating the key as needed, to reveal the flag.

> A single-byte XOR won't cut it here — the key is more than one character. Loop it: `key[i % len(key)]`.

---

## Submission

| Challenge | Flag |
|---|---|
| 1 — Cleartext Credentials | `CTF{________________}` |
| 2 — File Recovery | `CTF{________________}` |
| 3 — Covert Channel | `CTF{________________}` |
| 4 — Two Halves | `CTF{________________}` |
| 5 — Handshake + Payload | `CTF{________________}` |

---

## Hints (Use Only If Stuck!)

<details>
<summary>Challenge 1 — nudge</summary>
Filter `http.request.method == "POST"`, right-click the POST → *Follow → HTTP Stream*, and read the `username=…&password=…` body. From the terminal: `tcpdump -r creds.pcap -A | grep -i password`.
</details>

<details>
<summary>Challenge 2 — nudge</summary>
The request is `GET /flag.txt`. *File → Export Objects → HTTP* → select `flag.txt` → *Save*, then open it. Or `tcpdump -r download.pcap -A` and read the response body after the headers.
</details>

<details>
<summary>Challenge 3 — nudge</summary>
Filter `icmp`. The exfil packets are echo requests (type 8) whose data field holds printable text instead of the usual `abcdef…` filler; they share an unusual ICMP identifier and are numbered by sequence. Read their payloads in order and concatenate:
```bash
tshark -r icmp.pcap -Y 'icmp.type==8' -T fields -e data.text
```
</details>

<details>
<summary>Challenge 4 — nudge</summary>
Each POST body is Base64 (notice the `=` padding). Decode both and stick them together:
```bash
tcpdump -r packet_decode.pcap -A            # read the two bodies
echo '<part1 body>' | base64 -d ; echo
echo '<part2 body>' | base64 -d ; echo
# join the two outputs -> CTF{...}
```
</details>

<details>
<summary>Challenge 5 — nudge</summary>
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

- **Confusing display and capture filters.** Wireshark's bar uses `http`, `ip.addr==…`; tcpdump/BPF uses `tcp port 80`, `host …`.
- **Staring at single packets.** Reassemble — *Follow Stream* and *Export Objects* do the heavy lifting.
- **Ignoring the boring protocols.** ICMP and DNS look like noise, which is exactly why data hides in them.
- **Reading only one packet (Track B).** Challenges 4–5 need *both* payloads.
- **Forgetting to decode.** Extracted data is often Base64 or hex — finish with your Crypto toolkit.
- **Using a single-byte XOR in Challenge 5.** The key is multi-byte — repeat it across the data.

---

*Prepared by Coach Josh Brunty*
*Contact: [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu) | [coachbrunty@uscybergames.org](mailto:coachbrunty@uscybergames.org)*
*CTF Training Series*
