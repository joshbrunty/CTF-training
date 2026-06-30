# The Basics of Forensics for CTFs — Recovering What's Hidden

**CTF Training Series** · **Flag format:** `CTF{...}`
**Level:** Intermediate → challenging — you'll reuse `file`/`strings` from Linux, Base64/hex from Crypto, and the "absence is a clue" instinct from Reverse Engineering.

> This single README is both the **lesson** and the **lab**. Read the concepts, then work the four-artifact challenge pack (`DFIR-01`) in the second half — its flags aren't printed here on purpose. Work in your Kali VM and read *all* the output, not just the first line that matches.

---

## Learning Objectives

By the end of this session, you will be able to:

1. Explain what digital forensics recovers and the evidence-handling mindset.
2. Identify files by their **magic bytes**, not their extension, and explain why `file` can be fooled.
3. Read **metadata** (EXIF and friends) and separate real clues from decoys.
4. **Carve** embedded, appended, and deleted files out of a carrier with `binwalk`/`foremost`.
5. Analyze a **packet capture** in Wireshark — follow streams and spot exfiltration.
6. Hunt artifacts in a **memory dump** with `strings`, `grep`, and carving.

---

# Part 1 — The Lesson

## What Is Digital Forensics?

Forensics is recovering data that someone hid, deleted, or left behind — from files, disks, network captures, and memory — and doing it carefully enough that you can trust what you find.

In a CTF, that means: you're handed an artifact (an image, a `.pcap`, a disk or memory dump) and a flag is concealed inside. The skill is knowing **where data hides** and **which tool reveals it**.

> **Mindset:** look before you leap, and be thorough. Forensics challenges love **decoys** — the first flag-shaped string you find is often bait. Enumerate everything before you commit to an answer.

---

## File Signatures & Magic Bytes

A file's **extension is just a label** — it can lie. What a file *really* is comes from its first few bytes, the **magic number**:

| Bytes (hex) | Format |
|---|---|
| `FF D8 FF` | JPEG |
| `89 50 4E 47` | PNG (`.PNG`) |
| `50 4B 03 04` | ZIP (and `.docx`, `.apk`, `.jar`…) |
| `25 50 44 46` | PDF (`%PDF`) |
| `7F 45 4C 46` | ELF executable (`.ELF`) |

```bash
file mystery            # reads the header and names the real type
xxd mystery | head      # see the magic bytes yourself
```

> Magic bytes mark the **start** of a format. That's exactly why data can hide *after* a file ends — `file` only reads the header, so it never notices. (You'll exploit this in Challenge 2.)

---

## Metadata

Files carry hidden descriptive data. Images are the classic example — **EXIF** can hold the camera, GPS coordinates, timestamps, software, author, and free-form comment fields.

```bash
exiftool photo.jpg      # dump every metadata field
```

Flags love to hide in `UserComment`, `ImageDescription`, `Artist`, GPS, or XMP fields — sometimes plainly, sometimes Base64-encoded. Read **every** field; a challenge may plant a decoy in one and the real flag in another.

---

## File Carving

**Carving** is recovering files that are embedded in, appended to, or deleted from a carrier, by finding their signatures rather than relying on a filesystem.

- **Appended data** — a ZIP glued onto the end of a JPEG; the photo opens fine, the archive rides along behind it.
- **Embedded files** — a PNG inside a PDF, a file inside a disk image.
- **Deleted files** — still on disk until overwritten; carving finds them by signature.

```bash
binwalk suspect.bin        # scan for embedded signatures (newer Kali: binwalk3)
binwalk -e suspect.bin     # extract (v3 -> extractions/ , v2 -> _suspect.bin.extracted/)
foremost -i disk.img       # carve by signature into output/
```

> **Kali note:** newer Kali ships binwalk **v3** as the **`binwalk3`** command (the classic Python v2 is the `binwalk` package). Both work — just use whichever you have. v3 extracts into `extractions/`; v2 into `_<filename>.extracted/`.

---

## Steganography (a quick neighbor)

Steganography hides data *inside* media — least-significant-bit tweaks in an image, data in audio spectrograms, payloads behind a password in a JPEG.

- `steghide`, `zsteg`, `stegsolve`, and audio tools like Sonic Visualiser are the usual suspects.
- It overlaps carving: always run `strings`, `exiftool`, and `binwalk` on a suspicious image *first* — many "stego" challenges are really just appended data or metadata.

---

## Network Forensics

A packet capture (`.pcap`/`.pcapng`) is a recording of network traffic. **Wireshark** (GUI) and `tshark` (CLI) open it.

- **Follow the stream** — right-click a packet → *Follow → TCP/HTTP Stream* to read a whole conversation (credentials, transferred files, commands).
- **Filter to the signal** — `http`, `dns`, `ftp`, `dns.flags.response == 0`. Most traffic is noise; you're hunting the anomaly.
- **Exfiltration tells** — data leaving where it shouldn't: long high-entropy **DNS** subdomains, odd ports, Base64 in unexpected places. Attackers tunnel data out over DNS precisely because it's usually ignored.

---

## Memory Forensics

A memory dump is a snapshot of RAM — and RAM holds things the disk never does: running processes, command lines, environment variables, decrypted data, passwords, network connections.

- The deep tool is **Volatility** (`pslist`, `cmdline`, `hashdump`, …), which needs a real OS image to profile.
- But you get a long way with your everyday toolkit: `strings memory.raw | grep ...`, `xxd`, and carving with `binwalk`. Hunt for env vars, history, and credentials — and watch for decoys.

---

## The Forensics Workflow

1. **Identify** — `file` and `xxd`; what is this artifact, really?
2. **Inspect** — `strings`, `exiftool`, Wireshark; read everything before acting.
3. **Extract** — carve embedded files, follow streams, pull artifacts from the dump.
4. **Decode** — Base64/hex/XOR the recovered data (your Crypto skills).
5. **Recover** — the `CTF{...}` flag — and ignore the decoys along the way.

---

## Your Toolkit

| Tool | Purpose |
|---|---|
| `file`, `xxd` | Identify what an artifact really is; inspect bytes |
| `strings` | Pull readable text out of any file |
| `exiftool` | Read image/document metadata (EXIF/XMP) |
| `binwalk` / `binwalk3` | Detect and carve embedded files |
| `foremost` | Carve files by signature |
| `steghide`, `zsteg` | Steganography extraction |
| **Wireshark** / `tshark` | Packet-capture analysis |
| `Volatility` | Deep memory forensics (needs a profiled image) |
| `base64`, CyberChef | Decode recovered data |

> Some of these aren't installed on a minimal box. On Kali:
> ```bash
> sudo apt install binwalk3 foremost exiftool tshark
> ```
> (Newer Kali calls the v3 carver `binwalk3`; the classic `binwalk` package still works too.)

---

# Part 2 — The Lab: DFIR-01, Four Artifacts

## Scenario

An analyst workstation was flagged for suspicious activity. The responders pulled four artifacts off the box before it was wiped. Your job: examine each one and recover the flag it hides.

Each artifact is a **separate challenge** with its own flag. They get harder as you go — #1 is a warm-up, #4 will make you work.

> Treat these like real evidence: look before you leap, and read *all* of the output, not just the first thing that matches.

## Files Provided

| File | Challenge | Difficulty |
|---|---|---|
| [`briefing.jpg`](./briefing.jpg) | 1 — Metadata | Easy |
| [`evidence.jpg`](./evidence.jpg) | 2 — File Carving | Intermediate |
| [`capture.pcap`](./capture.pcap) | 3 — Network Forensics | Intermediate–Hard |
| [`memory.raw`](./memory.raw) | 4 — Memory | Intermediate–Hard |

---

### Challenge 1 — Metadata (`briefing.jpg`)

The first flag never made it into the *image* — it's in the file's **metadata**. Run `exiftool` (or `strings`) and read every field.

> Careful: not everything labeled like a flag *is* the flag. One field is a decoy; another holds the real thing in a slightly less convenient form.

### Challenge 2 — File Carving (`evidence.jpg`)

This one is hiding a whole **second file** appended after the image. A photo viewer shows a normal picture — but the bytes past the end of the JPEG are something else.

- Run `file` and `binwalk` (or `binwalk3`) on it. What does it report living inside?
- Carve it out — `binwalk -e` / `binwalk3 -e`, `foremost`, or even `unzip` directly on the `.jpg` — and read what's inside. (`unzip` works no matter which binwalk version you have.)

### Challenge 3 — Network Forensics (`capture.pcap`)

Open the capture in Wireshark. Most of the traffic is ordinary — but someone was **smuggling data out** of the network where it doesn't belong.

- Skim the protocols. The web and time-sync traffic is noise.
- Look hard at the **DNS** queries. A normal host doesn't look up names like `4354467b.exfil.evil-c2.net`.
- The data was **chunked across the subdomains** and sent in order. Collect the chunks, put them back together, and decode them back to text.

> Wireshark filter to get you started: `dns && dns.flags.response == 0`

### Challenge 4 — Memory (`memory.raw`)

A raw memory capture from the workstation. The flag is in here — but the **obvious** one is bait.

- `strings memory.raw | grep -i ctf` will find something quickly. Read it carefully before you submit it.
- The real flag isn't sitting in plaintext. Hunt around the process and environment artifacts — a shell-history line even tells you what to *do* with what you find.
- Once you've found the encoded value, decode it.

> This is a strings-and-carving exercise on a raw dump — your everyday toolkit (`strings`, `grep`, `xxd`, `base64`) is enough. No Volatility profile required.

---

## Submission

Four separate flags, one per artifact:

| Challenge | Flag |
|---|---|
| 1 — Metadata | `CTF{________________}` |
| 2 — Carving | `CTF{________________}` |
| 3 — Network | `CTF{________________}` |
| 4 — Memory | `CTF{________________}` |

---

## Hints (Use Only If Stuck!)

<details>
<summary>Challenge 1 — nudge</summary>

`exiftool briefing.jpg` and read the whole output. The **User Comment** field looks like gibberish ending in `=` — that's Base64. Decode it. The **Image Description** field is the decoy.

</details>

<details>
<summary>Challenge 2 — nudge</summary>

`binwalk evidence.jpg` (or `binwalk3 evidence.jpg`) reports a Zip archive after the JPEG data. Extract it with `binwalk -e evidence.jpg` (v3 → look in `extractions/`; v2 → in `_evidence.jpg.extracted/`), or simply `unzip evidence.jpg` — the Zip central directory is at the end of the file, so `unzip` finds it regardless of binwalk version. Read the extracted text file.

</details>

<details>
<summary>Challenge 3 — nudge</summary>

Filter to the `*.exfil.evil-c2.net` queries. The first label of each is 8 hex characters. Concatenate them in capture order, then convert the hex to ASCII:
```bash
tshark -r capture.pcap -Y 'dns.qry.name contains "exfil"' -T fields -e dns.qry.name \
  | cut -d. -f1 | tr -d '\n' | xxd -r -p; echo
```

</details>

<details>
<summary>Challenge 4 — nudge</summary>

`strings memory.raw | grep -i ctf` shows a **decoy**. Instead look for the environment variable:
```bash
strings memory.raw | grep SESSION_FLAG
```
Decode its Base64 value with `base64 -d`. (The bash-history line in the dump hints at exactly this.)

</details>

---

## Common Pitfalls

- **Trusting the extension.** `file` and `xxd` tell you what something *really* is.
- **Grabbing the first flag-shaped string.** Decoys are everywhere — read all the output.
- **Skipping the basics on an image.** `strings`/`exiftool`/`binwalk` solve more "stego" challenges than stego tools do.
- **Drowning in pcap noise.** Filter aggressively; the flag lives in the anomaly, not the bulk traffic.
- **Forgetting to decode.** Recovered data is often Base64 or hex — finish the job with your Crypto toolkit.

---

## Key Concepts Practiced

- **Magic bytes & file identification** — extension vs. reality
- **Metadata analysis** — EXIF fields, and why "looks like a flag" isn't "is the flag"
- **File carving** — appended data and recovering embedded files
- **Network forensics** — spotting DNS exfiltration and reassembling chunked data
- **Memory forensics** — extracting artifacts and credentials from a raw dump
- **Encoding** — recognizing and decoding Base64/hex, carried over from Cryptography

---

## Wrap-up & What's Next

**Recap:**
1. Identify by magic bytes, not extension.
2. Data hides in metadata, after a file's end, in packets, and in RAM — know the tool for each.
3. Stay thorough and skeptical: decoys punish the hasty.

**Next session (Guided Practice & Team Strategy):** putting all the categories together — working challenges as a team, dividing roles, and building toward a full CTF.

**Before then:**
1. Practice forensics on [picoCTF](https://picoctf.org) (Forensics track) — lots of metadata, carving, and pcap challenges.
2. Try a packet-analysis puzzle on [malware-traffic-analysis.net](https://www.malware-traffic-analysis.net) for real-world `.pcap` practice.
3. **Stretch:** install Volatility and run it against a public sample memory image to see process-level analysis.

---

## Resources

- **[Wireshark](https://www.wireshark.org)** — the packet-analysis standard.
- **[CyberChef](https://gchq.github.io/CyberChef/)** — decode recovered data fast.
- **[picoCTF](https://picoctf.org)** — beginner-friendly Forensics track.
- **[malware-traffic-analysis.net](https://www.malware-traffic-analysis.net)** — real `.pcap` exercises.
- **[Volatility Foundation](https://www.volatilityfoundation.org)** — memory forensics framework and sample images.

---

*Prepared by Coach Josh Brunty*
*Contact: [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu) | [coachbrunty@uscybergames.org](mailto:coachbrunty@uscybergames.org)*
*CTF Training Series*
