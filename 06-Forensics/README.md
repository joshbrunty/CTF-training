# Digital Forensics — Files, Disks & Metadata
 
**CTF Training Series** · **Flag format:** `CTF{...}`
**Level:** Intermediate → challenging — reuses `file`/`strings` from Linux and Base64/hex from Crypto, and adds disk-image analysis with **Autopsy** and **The Sleuth Kit**.
 
> This single README is both the **lesson** and the **lab**. Read the concepts in Part 1, then work the four artifacts in Part 2 — their flags aren't printed here on purpose. Work in your Kali VM and read *all* the output, not just the first line that matches.
 
> **Note:** *network* forensics (packet captures, Wireshark/tcpdump) is its own session — the **Networking & Packet Analysis** lesson. This session is about **files, disks, and metadata**.
 
---
 
## Learning Objectives
 
By the end of this session, you will be able to:
 
1. Identify files by their **magic bytes**, not their extension, and explain why `file` can be fooled.
2. Read **metadata** (EXIF and friends) and separate real clues from decoys.
3. Explain how a **file system** stores data — and why *deleting isn't erasing*.
4. **Carve** embedded and appended files out of a carrier with `binwalk`/`foremost`.
5. Analyze a **disk image** with **The Sleuth Kit** (`fls`, `icat`) and **Autopsy**, including recovering deleted files.
6. Extract artifacts from a **memory dump** with `strings` and `grep`.
---
 
# Part 1 — The Lesson
 
## What Is Digital Forensics?
 
Forensics is recovering data that someone hid, deleted, or left behind — from files, disks, and memory — carefully enough that you can trust what you find.
 
In a CTF, you're handed an artifact (an image, a disk image, a memory dump) and a flag is concealed inside. The skill is knowing **where data hides** and **which tool reveals it**.
 
> **Mindset:** look before you leap, and be thorough. Forensics challenges love **decoys** — the first flag-shaped string you find is often bait. Enumerate everything before you commit.
 
---
 
## File Signatures & Magic Bytes
 
A file's **extension is just a label** — it can lie. What a file *really* is comes from its first few bytes, the **magic number**:
 
| Bytes (hex) | Format |
|---|---|
| `FF D8 FF` | JPEG |
| `89 50 4E 47` | PNG |
| `50 4B 03 04` | ZIP (and `.docx`, `.apk`, `.jar`…) |
| `25 50 44 46` | PDF (`%PDF`) |
| `7F 45 4C 46` | ELF executable |
 
```bash
file mystery            # reads the header and names the real type
xxd mystery | head      # see the magic bytes yourself
```
 
> Magic bytes mark the **start** of a format — which is exactly why data can hide *after* a file ends. `file` only reads the header, so it never notices. (You'll use this in Challenge 2.)
 
---
 
## Metadata
 
Files carry hidden descriptive data. Images are the classic example — **EXIF** can hold the camera, GPS, timestamps, software, author, and free-form comment fields.
 
```bash
exiftool photo.jpg      # dump every metadata field
```
 
Flags love to hide in `UserComment`, `ImageDescription`, `Artist`, GPS, or XMP fields — sometimes plainly, sometimes Base64-encoded. Read **every** field; a challenge may plant a decoy in one and the real flag in another.
 
---
 
## How File Systems Store Data
 
A disk doesn't store "files" — it stores **blocks** (clusters) of data, plus bookkeeping that says which blocks belong to which file:
 
- A **directory entry** (FAT), **inode** (ext), or **MFT record** (NTFS) names a file and points to its data blocks.
- The disk is divided into **allocated** space (in use) and **unallocated** space (free).
- **Slack space** — the leftover between the end of a file and the end of its last cluster — can hold fragments of old data.
The key idea for forensics:
 
> **Deleting isn't erasing.** When you delete a file, the OS just marks its entry free and its blocks available — **the data stays on disk until something overwrites it.** That's why deleted files can be recovered, and why "I deleted it" is rarely the end of the story.
 
---
 
## File Carving
 
**Carving** recovers files embedded in, appended to, or deleted from a carrier by finding their signatures, without relying on the file system.
 
- **Appended data** — a ZIP glued onto the end of a JPEG; the photo opens fine, the archive rides behind it.
- **Embedded files** — a PNG inside a PDF, a file inside a disk image.
```bash
binwalk suspect.bin        # scan for embedded signatures (newer Kali: binwalk3)
binwalk -e suspect.bin     # extract (v3 -> extractions/ , v2 -> _suspect.bin.extracted/)
foremost -i disk.img       # carve by signature into output/
unzip carrier.jpg          # appended ZIPs open directly
```
 
---
 
## Disk Image Analysis with Sleuth Kit & Autopsy
 
A **disk image** (`.dd`, `.raw`, `.E01`) is a byte-for-byte copy of a drive. Two ways to work it:
 
**The Sleuth Kit (TSK)** — command-line tools that read the image *below* the OS:
 
```bash
mmls disk.img              # partition layout (offsets) — for full-disk images
fsstat disk.img            # file-system type, label, layout
fls disk.img               # list files; DELETED files are marked with a *
icat disk.img <inode>      # dump a file's contents by its inode/entry number
istat disk.img <inode>     # metadata for one file
```
 
- `fls` shows deleted entries alongside live ones — a `*` marks a deleted file, followed by its inode.
- `icat` (add `-r` to recover) dumps that inode's data — how you pull a deleted file back.
**[Autopsy](https://www.autopsy.com/)** — the free GUI that wraps TSK (Windows, Linux, Mac):
 
1. *New Case* → add the disk image as a **data source**.
2. Let ingest run, then browse the **file tree**. Deleted files show with a marker (often a red ✗).
3. Right-click a deleted file → **Extract** to recover it; use **Keyword Search** to hunt for `CTF{`.
> Autopsy and `fls`/`icat` do the *same thing* — Autopsy is the point-and-click view of what TSK does on the command line.
 
---
 
## Memory Forensics (bonus)
 
A memory dump is a snapshot of RAM — it holds running processes, command lines, environment variables, and passwords the disk never sees. The deep tool is **Volatility**, but your everyday `strings memory.raw | grep …` gets a long way. Watch for decoys.
 
> Want the full Volatility workflow? See the **[optional memory-forensics lab](#-optional-extra-practice--memory-forensics-with-volatility)** at the end of this README.
 
---
 
## The Forensics Workflow
 
1. **Identify** — `file` and `xxd`; what is this artifact, really?
2. **Inspect** — `strings`, `exiftool`, `fls`, Autopsy; read everything before acting.
3. **Extract** — carve embedded files, recover deleted files, pull artifacts.
4. **Decode** — Base64/hex the recovered data (your Crypto skills).
5. **Recover** — the `CTF{...}` flag — and ignore the decoys along the way.
---
 
## Your Toolkit
 
| Tool | Purpose |
|---|---|
| `file`, `xxd`, `strings` | Identify and read any file |
| `exiftool` | Image/document metadata |
| `binwalk` / `binwalk3`, `foremost` | Carve embedded files |
| **The Sleuth Kit** (`mmls`, `fsstat`, `fls`, `icat`, `istat`) | Disk-image analysis from the CLI |
| **Autopsy** | GUI disk forensics — browse, recover deleted files, keyword search |
| `strings` + `base64` | Memory artifacts and decoding |
 
> On Kali: `sudo apt install sleuthkit autopsy binwalk3 foremost exiftool`. (Newer Kali calls the v3 carver `binwalk3`; the classic `binwalk` package still works too.)
 
---
 
# Part 2 — The Lab: Four Artifacts
 
## Scenario
 
A workstation was seized after suspicious activity. Responders imaged the drive and pulled several artifacts. Each hides a flag. They ramp up: #1 is a warm-up, #3 is the disk-forensics centerpiece, #4 is a bonus for the fast finishers.
 
## Files Provided
 
| File | Challenge | Difficulty |
|---|---|---|
| [`briefing.jpg`](https://github.com/joshbrunty/CTF-training/raw/main/06-Forensics/briefing.jpg) | 1 — Metadata | Easy |
| [`evidence.jpg`](https://github.com/joshbrunty/CTF-training/raw/main/06-Forensics/evidence.jpg) | 2 — File Carving | Intermediate |
| [`case001.dd`](https://github.com/joshbrunty/CTF-training/raw/main/06-Forensics/case001.dd) | 3 — Deleted-File Recovery | Intermediate–Hard |
| [`memory.raw`](https://github.com/joshbrunty/CTF-training/raw/main/06-Forensics/memory.raw) | 4 — Memory (bonus) | Intermediate–Hard |
 
> Click any file above to download it, then analyze it in your Kali VM.
 
---
 
### Challenge 1 — Metadata (`briefing.jpg`)
 
The flag never made it into the *image* — it's in the file's **metadata**. Run `exiftool` (or `strings`) and read every field.
 
> Careful: not everything labeled like a flag *is* the flag. One field is a decoy; another holds the real thing in a slightly less convenient form.
 
### Challenge 2 — File Carving (`evidence.jpg`)
 
This one hides a whole **second file** appended after the image. A viewer shows a normal picture; the bytes past the end of the JPEG are something else.
 
- Run `file` and `binwalk` (or `binwalk3`) on it. What does it report living inside?
- Carve it out — `binwalk -e` / `binwalk3 -e`, `foremost`, or even `unzip` directly on the `.jpg` — and read what's inside.
### Challenge 3 — Deleted-File Recovery (`case001.dd`) 🚩
 
`case001.dd` is a raw **disk image** of the suspect's USB drive. The important file was **deleted before the drive was seized** — but *deleting isn't erasing.*
 
- Load it in **Autopsy** (add it as a data source), **or** use **The Sleuth Kit** from the terminal.
- List the files: `fls case001.dd`. Note the live files — and the one marked **deleted** (`*`).
- **Recover** the deleted file by its entry number and read it.
- Don't be fooled by what the *visible* files say.
> This is the core disk-forensics skill: recovering what someone tried to get rid of.
 
### Challenge 4 — Memory (`memory.raw`) — bonus
 
A raw memory capture. The **obvious** flag is bait; the real one is encoded. `strings memory.raw | grep -i ctf` finds the decoy — hunt the process/environment artifacts for the real, encoded value, then decode it. (No Volatility profile required.)
 
---
 
## Submission
 
| Challenge | Flag |
|---|---|
| 1 — Metadata | `CTF{________________}` |
| 2 — Carving | `CTF{________________}` |
| 3 — Deleted-File Recovery | `CTF{________________}` |
| 4 — Memory (bonus) | `inCTF{________________}` |
 
---
 
## Hints (Use Only If Stuck!)
 
<details>
<summary>Challenge 1 — nudge</summary>
`exiftool briefing.jpg` and read the whole output. The **User Comment** field looks like gibberish ending in `=` — that's Base64. Decode it. The **Image Description** field is the decoy.
 
</details>
<details>
<summary>Challenge 2 — nudge</summary>
`binwalk evidence.jpg` (or `binwalk3 evidence.jpg`) reports a Zip after the JPEG. Extract with `binwalk -e` (v3 → `extractions/`; v2 → `_evidence.jpg.extracted/`), or just `unzip evidence.jpg` — the Zip's directory is at EOF, so `unzip` finds it regardless. Read the extracted text file.
 
</details>
<details>
<summary>Challenge 3 — nudge</summary>
Sleuth Kit from the terminal:
```bash
fsstat case001.dd            # confirm it's a FAT file system
fls case001.dd               # a file is marked deleted with a *  (note its number)
icat -r case001.dd <number>  # recover that deleted file's contents
```
In **Autopsy**: add `case001.dd` as a data source, open the volume's file list, find the deleted file (red ✗), right-click → *Extract* (or just read it in the viewer). The visible files are there to mislead — the flag is in the deleted one.
 
</details>
<details>
<summary>Challenge 4 — nudge</summary>
`strings memory.raw | grep -i ctf` shows a **decoy**. Look instead for the environment variable: `strings memory.raw | grep SESSION_FLAG`, then `base64 -d` its value. (A bash-history line in the dump hints at exactly this.)
 
</details>
---
 
## 🧠 Optional Extra Practice — Memory Forensics with Volatility
 
*Advanced · bonus · for fast finishers. Best attempted after the four artifacts above.*
 
Challenge 4 warmed you up on a RAM capture with `strings`. This optional lab does the real thing — full **memory forensics** with **[Volatility](https://www.volatilityfoundation.org/)**, the industry standard for analyzing RAM.
 
> **Attribution:** adapted from **MemLabs — Lab 3, "The Evil's Den,"** by **P. Abhiram Kumar ([@stuxnet999](https://github.com/stuxnet999/MemLabs))**. The scenario and worksheet here are rewritten for this course; the memory image is the original from MemLabs — please credit the author and use the original link below.
 
**Scenario.** A workstation "started acting weird," and a **RAM capture** was taken before it was wiped. The user's account: *"Some script scrambled a text file I care about — now it's just gibberish. And I think whoever did it left a picture on the desktop that doesn't belong."* Recover the secret: a single flag in **two halves**, where the **first half unlocks the second**.
 
**Setup**
- **Volatility** — on Kali: `sudo apt install volatility3` (command `vol`; may be `vol.py` / `volatility3`). Classic **Volatility 2** also works and is what most references use.
- **steghide** — `sudo apt install steghide`.
- **The image** `MemoryDump_Lab3.raw` (~1 GB) is **not stored in this repo**. Download it from the [MemLabs Lab 3 page](https://github.com/stuxnet999/MemLabs), or get it from your instructor on the lab network.
**Work the phases in order:**
 
1. **Identify** the image — what OS/build is it? *(Volatility 3 auto-detects; Volatility 2 needs a profile.)*
2. **Processes → command lines.** List the processes, then read their command lines. One everyday Windows app was opened **twice**, each time with a Desktop file — one a `.py` script, one a `.txt`. Note both.
3. **Recover the first half.** Dump both files out of memory. The **script** shows *how* the text was scrambled — an **XOR + Base64** chain (straight from the Crypto session). Reverse those steps on the **scrambled file** to get the first fragment.
4. **Recover the second half.** Find and dump the suspicious **image**, then look inside it with `steghide`. **The passphrase is the first fragment you just recovered — use it exactly as it appears.** `steghide` writes a small file containing the second fragment.
5. **Assemble & submit.**
> **Flag-format note:** this is a real-world image whose *internal* secret uses a slightly different wrapper. Recover the full content and submit it as **`inCTF{...}`** (same characters, different wrapper).
 
**Submission:** `inCTF{________________________}`
 
<details>
<summary>Hints — Volatility commands</summary>
```bash
# 1) identify the image
vol -f MemoryDump_Lab3.raw windows.info          # Volatility 3
volatility -f MemoryDump_Lab3.raw imageinfo      # Volatility 2 -> use the suggested Win7 x86 profile
 
# 2) processes + command lines
vol -f MemoryDump_Lab3.raw windows.pstree
vol -f MemoryDump_Lab3.raw windows.cmdline       # Vol2: --profile=<profile> pslist / cmdline
#   -> notepad.exe opened  ...\Desktop\evilscript.py  and  ...\Desktop\vip.txt
 
# 3) dump both files, then reverse the script's XOR+Base64 on the scrambled file
vol -f MemoryDump_Lab3.raw windows.filescan | grep -Ei "evilscript|vip.txt"
vol -f MemoryDump_Lab3.raw -o . windows.dumpfiles --virtaddr <offset>
#   the script XORs each char with a small number, then Base64-encodes.
#   to reverse -> Base64-decode, then XOR each byte with the same number (a few lines of Python)
 
# 4) dump the suspicious .jpeg, then steghide with the first fragment as the passphrase
vol -f MemoryDump_Lab3.raw windows.filescan | grep -i ".jpeg"
#   ...dumpfiles on its offset (as above)...
steghide extract -sf <dumped>.jpeg -p '<first-fragment-verbatim>'
cat 'secret text'
```
Carving the JPEG with `foremost`/`binwalk` **won't** work here — it's fragmented across memory pages, so `steghide` rejects the bytes. Use Volatility's `dumpfiles`, which reconstructs the real file.
 
</details>
---
 
## Common Pitfalls
 
- **Trusting the extension.** `file` and `xxd` tell you what something *really* is.
- **Grabbing the first flag-shaped string.** Decoys are everywhere — read all the output.
- **Thinking "deleted" means gone.** `fls` shows deleted entries; `icat` brings them back.
- **Skipping the basics on an image.** `strings`/`exiftool`/`binwalk` solve more "stego" challenges than stego tools do.
- **Forgetting to decode.** Recovered data is often Base64 or hex — finish with your Crypto toolkit.
---
 
## Key Concepts Practiced
 
- **Magic bytes & file identification** — extension vs. reality
- **Metadata analysis** — EXIF, and telling a clue from a decoy
- **File-system basics** — allocated vs. unallocated space, and why deleting ≠ erasing
- **Deleted-file recovery** — The Sleuth Kit (`fls`/`icat`) and Autopsy
- **File carving** — recovering embedded/appended files
- **Memory artifacts** — pulling secrets from a raw dump
- **Memory forensics with Volatility** *(optional)* — process/command-line/file analysis of a RAM image
---
 
## Wrap-up & What's Next
 
**Recap:**
1. Identify by magic bytes, not extension.
2. Data hides in metadata, after a file's end, in unallocated space, and in RAM.
3. Deleting isn't erasing — Autopsy and The Sleuth Kit bring deleted files back.
**Next session (Reverse Engineering):** taking apart a compiled binary — `file`, `strings`, `objdump`, and Ghidra — to recover what's hidden inside.
 
**Before then:**
1. Practice on [picoCTF](https://picoctf.org) (Forensics track) — metadata, carving, and disk challenges.
2. Install **[Autopsy](https://www.autopsy.com/)** and re-open `case001.dd` — browse the file tree and recover the deleted file in the GUI.
3. **Stretch:** run Volatility against a public sample memory image.
---
 
## Resources
 
- **[Autopsy](https://www.autopsy.com/)** + **[The Sleuth Kit](https://www.sleuthkit.org/)** — GUI and CLI disk forensics.
- **[CyberChef](https://gchq.github.io/CyberChef/)** — decode recovered data fast.
- **[picoCTF](https://picoctf.org)** — beginner-friendly Forensics track.
- **[Volatility Foundation](https://www.volatilityfoundation.org)** — memory forensics framework and sample images.
---
 
*Prepared by Coach Josh Brunty*
*Contact: [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu) | [coachbrunty@uscybergames.org](mailto:coachbrunty@uscybergames.org)*
*CTF Training Series*
