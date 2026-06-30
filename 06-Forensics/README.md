# DFIR-01: Four Artifacts
**Category:** Digital Forensics | **Difficulty:** Intermediate → Challenging | **Flag Format:** `CTF{...}`

---

## Scenario

An analyst workstation was flagged for suspicious activity. The responders pulled four artifacts off the box before it was wiped. Your job: examine each one and recover the flag it hides.

Each artifact is a **separate challenge** with its own flag. They get harder as you go — #1 is a warm-up, #4 will make you work.

> **Work everything in your Linux VM.** Treat these like real evidence: look before you leap, and read *all* of the output, not just the first thing that matches.

---

## Files Provided

| File | Challenge | Difficulty |
|---|---|---|
| [`briefing.jpg`](./briefing.jpg) | 1 — Metadata | Easy |
| [`evidence.jpg`](./evidence.jpg) | 2 — File Carving | Intermediate |
| [`capture.pcap`](./capture.pcap) | 3 — Network Forensics | Intermediate–Hard |
| [`memory.raw`](./memory.raw) | 4 — Memory | Intermediate–Hard |

---

## Recommended Tools

| Tool | Purpose |
|---|---|
| `file` | Identify what each artifact really is |
| `exiftool` | Read image metadata (EXIF/XMP) |
| `strings`, `xxd` | Pull readable text / inspect bytes |
| `binwalk`, `foremost` | Detect and carve embedded files |
| `unzip` | Extract archives |
| **Wireshark** / `tshark` | Analyze the packet capture |
| `base64`, CyberChef | Decode encoded data |

> A few of these aren't installed by default on a minimal box. On Kali: `sudo apt install binwalk3 foremost exiftool tshark`.
>
> **Heads-up on binwalk:** newer Kali ships binwalk **v3**, installed as the **`binwalk3`** command (the classic Python v2 is still available as the `binwalk` package). Both work for this pack — just use whichever you have. One difference: **v3 extracts into an `extractions/` folder**, while **v2 uses `_<filename>.extracted/`**.

---

## Challenge 1 — Metadata (`briefing.jpg`)

The first flag never made it into the *image* — it's in the file's **metadata**. Run `exiftool` (or `strings`) and read every field.

> Careful: not everything labeled like a flag *is* the flag. One field is a decoy; another holds the real thing in a slightly less convenient form.

---

## Challenge 2 — File Carving (`evidence.jpg`)

`briefing.jpg` was just metadata. This one is hiding a whole **second file** appended after the image. A photo viewer shows a normal picture — but the bytes past the end of the JPEG are something else.

- Run `file` and `binwalk` (or `binwalk3`) on it. What does it report living inside?
- Carve it out — `binwalk -e` / `binwalk3 -e`, `foremost`, or even `unzip` directly on the `.jpg` — and read what's inside. (`unzip` works no matter which binwalk version you have.)

---

## Challenge 3 — Network Forensics (`capture.pcap`)

Open the capture in Wireshark. Most of the traffic is ordinary — but someone was **smuggling data out** of the network where it doesn't belong.

- Skim the protocols. The web and time-sync traffic is noise.
- Look hard at the **DNS** queries. A normal host doesn't look up names like `4354467b.exfil.evil-c2.net`.
- The data was **chunked across the subdomains** and sent in order. Collect the chunks, put them back together, and decode them back to text.

> Wireshark filter to get you started: `dns && dns.flags.response == 0`

---

## Challenge 4 — Memory (`memory.raw`)

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

## Key Concepts Practiced

- **Metadata analysis** — EXIF fields, and why "looks like a flag" isn't "is the flag"
- **File carving** — magic bytes, appended data, and recovering embedded files
- **Network forensics** — spotting DNS exfiltration and reassembling chunked data
- **Memory forensics** — extracting artifacts and credentials from a raw dump
- **Encoding** — recognizing and decoding Base64/hex, carried over from Cryptography

---

*Prepared by Coach Josh Brunty*
*Contact: [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu) | [coachbrunty@uscybergames.org](mailto:coachbrunty@uscybergames.org)*
