# CTF Training

<p align="center">
  <img src="assets/CTF-logo.png" alt="CTF Training Logo" width="350">
</p>
 
A hands-on, **8-part Capture the Flag (CTF) curriculum** that takes participants from *"what's a flag?"* to competing as a team under a clock. Built for the **United States & Marshall University Cyber Team** and delivered as an intensive summer institute at the **University of Prishtina (Kosovo)**, every module pairs a plain-language lesson with self-contained, verified challenges — so you learn a technique and immediately use it.
 
The curriculum builds the skills that place in the **[National Cyber League (NCL)](https://nationalcyberleague.org)**, **[US Cyber Games](https://www.uscybergames.com)**, and collegiate CTFs — the same techniques you'll use on game day and on the job in digital forensics and incident response.
 
---
 
## The Curriculum
 
Ten modules, one per day. Each folder is a **single README that is both the lesson and the lab**, plus any challenge files.
 
| Day | Module | What you'll learn | Hands-on |
|---|---|---|---|
| 1 | [Introduction to CTFs](./01-Introduction-to-CTFs/) | Formats, scoring, flag types, and the CTF mindset | Encoding warm-ups |
| 2 | [Linux & Command-Line](./02-Linux-and-Command-Line/) | Navigation, pipes, `grep`, permissions, scripting | CLI puzzles |
| 3 | [Cryptography Essentials](./03-Cryptography-Essentials/) | Hashing, encoding, classical ciphers, XOR | 4 crypto challenges |
| 4 | [Web Exploitation](./04-Web-Exploitation/) | SQL injection, XSS, recon, tokens (JWT) | Source, token & lab challenges |
| 5 | [Networking & Packet Analysis](./05-Networking-and-Packet-Analysis/) | Wireshark & `tcpdump`, follow streams, spot exfil | 3 packet captures |
| 6 | [Digital Forensics](./06-Forensics/) | Metadata, carving, **Autopsy / The Sleuth Kit**, deleted-file recovery | 4 artifacts (incl. a disk image) |
| 7 | [Reverse Engineering](./07-Reverse-Engineering/) | `file`/`strings`/`objdump`, Ghidra, XOR obfuscation | The **Vault** binary |
| 8 | [Practice & Guided Labs](./08-Team-Strategy-and-Practice/) | Working challenges across every category, together | Cross-category practice |
| 9 | [Team Strategy & Final Prep](./08-Team-Strategy-and-Practice/) | Scoring, triage, roles, collaboration, stamina | Mock CTF run |
| 10 | Final CTF & Wrap-Up | The live competition, then debrief and next steps | Full-length team CTF |
 
> Each hands-on flag is meant to be solved yourself — the public READMEs are answer-free, with optional gated hints. **Instructor solution guides live in a separate private repository** (see [Instructor Access](#instructor-access)).
 
---
 
## How Each Module Works
 
- **One README per folder** — read *Part 1 (the lesson)*, then work *Part 2 (the lab)*.
- **Challenge files** (binaries, captures, disk images) sit alongside the README; click a file to download it and analyze it in your VM.
- **Progressive difficulty** — each module ramps from an approachable warm-up to a challenge that stretches stronger players, so mixed-skill teams all stay engaged.
- **Everything connects** — encoding from Day 3 resurfaces inside a binary on Day 7; the "read all the output" discipline from forensics pays off everywhere.
---
 
## Tools & Environment
 
Challenges are designed for **[Kali Linux](https://www.kali.org/)**. Across the curriculum you'll use industry-standard tools:
 
| Area | Tools |
|---|---|
| **Recon / CLI** | `file`, `strings`, `xxd`, `grep`, `bash` |
| **Cryptography** | `base64`, `hashcat` / `john`, [CyberChef](https://gchq.github.io/CyberChef/), `python3` |
| **Web** | Browser DevTools, [Burp Suite](https://portswigger.net/burp), `curl`, `sqlmap` |
| **Networking** | [Wireshark](https://www.wireshark.org), `tcpdump`, `tshark` |
| **Forensics** | `exiftool`, `binwalk`, `foremost`, [Autopsy](https://www.autopsy.com/), [The Sleuth Kit](https://www.sleuthkit.org/) |
| **Reverse Engineering** | `objdump`, [Ghidra](https://ghidra-sre.org), `gdb`, `python3` |
 
> **Apple Silicon users:** some challenges use x86-64 binaries. If `objdump` returns `can't disassemble for architecture UNKNOWN`, install the cross-platform binutils:
> ```bash
> sudo apt install binutils-x86-64-linux-gnu
> x86_64-linux-gnu-objdump -d <binary>
> ```
> Ghidra and Autopsy are unaffected — they run on any host platform (Windows, macOS, Linux).
 
---
 
## Flag Format
 
All challenges use the format **`CTF{...}`**.
 
Submit flags exactly as they appear, wrapper and all — scorers are exact-match, so `CTF{...}` with a stray space or newline won't count.
 
---
 
## Repo Structure
 
```
CTF-training/
├── README.md
├── 01-Introduction-to-CTFs/
│   └── README.md
├── 02-Linux-and-Command-Line/
│   └── README.md
├── 03-Cryptography-Essentials/
│   └── README.md
├── 04-Web-Exploitation/
│   └── README.md
├── 05-Networking-and-Packet-Analysis/
│   ├── README.md
│   ├── creds.pcap
│   ├── download.pcap
│   └── icmp.pcap
├── 06-Forensics/
│   ├── README.md
│   ├── briefing.jpg
│   ├── evidence.jpg
│   ├── case001.dd
│   └── memory.raw
├── 07-Reverse-Engineering/
│   ├── README.md
│   └── vault
└── 08-Team-Strategy-and-Practice/
    └── README.md
```
 
---
 
## About This Series
 
These materials were developed by **Dr. Josh Brunty**, Professor of Cyber Forensics & Security at [Marshall University](https://www.marshall.edu/cyber) and Head Coach of the [US Cyber Team](https://www.uscyberteam.com). They bridge the gap between classroom knowledge and competition performance, with a focus on techniques that apply equally in real-world digital forensics and incident response — and have been delivered internationally as a summer institute at the University of Prishtina, Kosovo.
 
---
 
## Instructor Access
 
Public READMEs are answer-free. **Instructor solution guides, answer keys, and challenge source** are maintained in a separate private repository. If you are an instructor and need access, contact [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu).
 
---
 
## Contributing
 
Have a challenge idea or want to contribute a walkthrough? Open an issue or reach out directly at [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu) | [coachbrunty@uscybergames.org](mailto:coachbrunty@uscybergames.org).
 
