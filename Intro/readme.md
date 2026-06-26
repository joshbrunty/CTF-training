# Introduction to CTFs and Cybersecurity Fundamentals

**CTF Training Series**
**Level:** Intermediate-friendly — no deep security background assumed, but comfort with a terminal and a willingness to move at a brisk pace will help.

> Your reference for Day 1. Work through the warm-up flags yourself — the answers aren't printed here on purpose. Today needs only a web browser; before the next session you'll set up a Linux environment (see **Environment Setup**).

---

## Learning Objectives

By the end of this session, you will be able to:

1. Define a Capture the Flag (CTF) competition and distinguish the main formats (Jeopardy, Attack-Defense, King of the Hill).
2. Describe the standard CTF challenge categories and the skill each one trains.
3. Apply a repeatable **solve workflow** (recon → identify → exploit → recover → document) to an unfamiliar challenge.
4. Explain the **CIA triad** and the distinction between a vulnerability, an exploit, and a payload.
5. Articulate the **legal and ethical boundaries** of security work and why a CTF is a lawful sandbox.
6. Recognize common encoding layers on sight and **submit a flag** in the correct format.

---

## What is a CTF?

### The core idea

A **Capture the Flag** is a competition where you solve technical challenges to recover a secret string — the **flag** — and submit it for points. The flag proves you solved it, and is wrapped in a recognizable format:

```
CTF{th1s_1s_what_a_flag_looks_l1ke}
```

Submission is **exact match** — case, underscores, and the wrapper all count. Copy-paste your flags; don't retype them.

### A little history

CTFs grew out of hacker-conference culture; the **DEF CON CTF** (mid-1990s) is the most famous and longest-running. They've since moved fully mainstream — universities, militaries, and major tech companies run and sponsor them, with thousands of events a year tracked on [CTFtime.org](https://ctftime.org).

### Formats


| Format               | How it works                                                                                         | Where you'll see it                                |
| -------------------- | ---------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| **Jeopardy**         | A board of challenges grouped by category and point value. Solve independently, in any order.        | Most online CTFs, picoCTF, NCL, this series' final |
| **Attack-Defense**   | Every team runs identical vulnerable services. Patch yours while exploiting opponents' in real time. | DEF CON finals, iCTF; infrastructure-heavy         |
| **King of the Hill** | Compromise a shared target and *hold* it against other teams.                                        | Live events, some online platforms                 |
| **Mixed / hybrid**   | A combination of the above.                                                                          | Larger flagship events                             |


This series is **Jeopardy-style** — the format that maps directly to the [National Cyber League (NCL)](https://nationalcyberleague.org) and most competitions you'll enter.

### Scoring models

- **Static** — a challenge is always worth its listed points.
- **Dynamic (decay)** — points start high and drop as more teams solve it, rewarding early and hard solves.

---

## The Solve Workflow

CTFs reward a *method*, not just knowledge. Most challenges, in any category, follow the same loop:

1. **Recon** — What are you actually looking at? Run the cheap, non-destructive checks first (file type, strings, headers, page source, traffic). Gather before you act.
2. **Identify** — What *kind* of problem is this? An encoding? A known web bug class? A weak cipher? Naming the category narrows the toolset.
3. **Exploit / Solve** — Apply the technique. Iterate quickly; small experiments beat big guesses.
4. **Recover the flag** — Pull the `CTF{...}` string out and **submit it exactly**.
5. **Document** — Jot the steps, the tools, and the key insight *as you go*.

> **Build this habit from day one:** keep a running notes file per challenge. Strong competitors are relentless note-takers, and your write-ups become your personal playbook.

---

## Tour of CTF Categories

A fast map of the field. You don't need to understand the techniques yet — each one gets a dedicated day later.


| Category                         | What you're doing                                    | Example                                                          | Covered        |
| -------------------------------- | ---------------------------------------------------- | ---------------------------------------------------------------- | -------------- |
| **Cryptography**                 | Breaking or decoding encoded / weakly-encrypted data | Decode layered Base64; crack a classic cipher; spot a reused key | Day 3          |
| **Web Exploitation**             | Abusing flaws in web applications                    | SQL injection, cross-site scripting (XSS), broken auth, IDOR     | Day 4          |
| **Networking / Packet Analysis** | Reading captured traffic                             | Recover a credential or file from a `.pcap`                      | Day 5          |
| **Digital Forensics**            | Recovering evidence from files, disks, memory        | Carved/deleted files, metadata, steganography                    | Day 6          |
| **Reverse Engineering**          | Understanding what a compiled program does           | Disassemble/decompile a binary to find a password check          | Day 7          |
| **Binary Exploitation (pwn)**    | Hijacking a running program's behavior               | Buffer overflow to redirect execution                            | Intro on Day 7 |
| **OSINT**                        | Intelligence from public data                        | Geolocate a photo; pivot from a username                         | Woven in       |
| **Misc / Programming**           | Scripting, automation, oddball puzzles               | Automate 1,000 transformations against a timed server            | Throughout     |


> **Key distinction:** **encoding is not encryption.** Base64, hex, and ROT13 are reversible *by design* with no secret — they represent data, they don't protect it. Encryption needs a key. A large share of beginner "crypto" challenges are pure encoding, often **stacked in layers** — as today's warm-up shows.

---

## Cybersecurity Fundamentals

### The CIA triad

Every security concept ladders up to three goals:

- **Confidentiality** — only authorized parties can read the data. *(Broken by data theft, eavesdropping, weak crypto.)*
- **Integrity** — data isn't altered without authorization. *(Broken by tampering, injection.)*
- **Availability** — systems and data are reachable when needed. *(Broken by denial-of-service, ransomware.)*

Every challenge category attacks one of these. The triad is the *why* behind the *what*.

### Core vocabulary

- **Asset** — something worth protecting (data, a server, an account).
- **Vulnerability** — a weakness (e.g., an input field that doesn't validate what you type).
- **Exploit** — the technique or code that *uses* a vulnerability.
- **Payload** — what the exploit actually delivers or does.
- **Threat actor** — who might attack (criminal, insider, nation-state — or you, inside a CTF).
- **Attack surface** — every point where someone could try to get in.

> Worth remembering: *"The vulnerability is the unlocked window. The exploit is climbing through it. The payload is what you do once you're inside."*

### Red / Blue / Purple

- **Red** = offense (find and exploit weaknesses).
- **Blue** = defense (detect, harden, respond).
- **Purple** = the feedback loop between them.

CTFs train mostly **red-team** thinking — but understanding the attacker is what makes a strong defender, which is where most of this work leads professionally.

---

## Ethics & the Law

This is the price of admission to the field. Read it carefully.

1. **CTFs are the legal sandbox.** Every target in this series is one we own, host, or explicitly authorize. The skills are real; the *permission* is what makes practicing them lawful. The same command is a class exercise here and a **crime** against a system you don't own.
2. **Authorization is everything.** Professional security testing happens only with **written permission** and a defined **scope** (rules of engagement). No permission, no scope → don't touch it.
3. **The law is real.** Unauthorized access to computer systems is criminal under laws in essentially every country (in the U.S., the Computer Fraud and Abuse Act; comparable statutes exist worldwide). *"I was just curious"* is not a defense.
4. **Responsible disclosure.** If you find a real vulnerability outside a CTF, the ethical path is to report it **privately** to the owner — not to exploit it, and not to post it publicly.
5. **Stay in the game.** Don't disrupt other competitors, deface infrastructure, or pull flags outside the rules. Sportsmanship is part of the culture.

> **Think about:** You discover your university's student portal has a flaw that exposes everyone's grades. What do you do — and what do you *not* do? (Hint: report privately; document; never log into anyone else's account to "prove" it.)

---

## Environment Setup

**Day 1 needs only a web browser** — we use [CyberChef](https://gchq.github.io/CyberChef/), so don't worry if you're not set up yet today. But you must have **one** of the options below working **before the next (Linux) session.** Pick the one that fits your machine, and **start the download tonight** — the Kali image is several GB.


| Option                                    | Best for                                     | Notes                                                                                                                                                                                                        |
| ----------------------------------------- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Kali in a VM** *(recommended for most)* | Windows or macOS                             | Download the prebuilt Kali image from [kali.org](https://www.kali.org/get-kali/) and import it into **VirtualBox** (free) or VMware. Fully isolated from your host — the safe place to open untrusted files. |
| **WSL** (Windows Subsystem for Linux)     | Windows users who want something lightweight | In an **admin** PowerShell: `wsl --install`, or `wsl --install -d kali-linux`. Gives you a real Linux shell fast. Great for command-line work; GUI tools (Wireshark, Ghidra, Burp) are clunkier here.        |
| **Native Kali**                           | A spare machine or dual-boot                 | Best performance, biggest commitment. From [kali.org](https://www.kali.org/get-kali/).                                                                                                                       |
| **Browser-only fallback**                 | If you get stuck before next session         | picoCTF, TryHackMe, and CyberChef run entirely in-browser. Use this so you're never blocked — but aim to get a real Linux environment going.                                                                 |


- **Apple Silicon (M-series) Macs:** use the **ARM64** Kali image; a few x86-only tools need emulation (we'll hit this on the reverse-engineering day).
- **Intel Mac / Linux host:** the standard x86-64 Kali image is fine.
- **Always** analyze unknown files inside the VM, never on your host machine.

### Tools you'll meet in this course

- **Kali Linux / Parrot OS** — security-focused Linux distributions, pre-loaded with tools.
- **CyberChef** — browser "Swiss-army knife" for encoding/decoding/crypto (today).
- **Wireshark / tcpdump** — network packet analysis.
- **Autopsy / Sleuth Kit** — digital forensics.
- **Ghidra** — reverse engineering / decompilation.
- **Burp Suite** — web application testing.
- **Python** — the glue that automates everything.

---

## Hands-on: Warm-up Flags 🚩

Decode each string to recover a flag in the form `CTF{...}`. The first three are quick; the fourth has more than one layer. Solve them in [CyberChef](https://gchq.github.io/CyberChef/) (browser) or your terminal.

**Flag 1 — Base64**

```
Q1RGe3czbGMwbTNfdDBfdGgzX2c0bTN9
```

CyberChef: **From Base64** · Terminal: `echo '<string>' | base64 -d`

**Flag 2 — Hexadecimal**

```
4354467b6833785f31735f6a7573745f62797433737d
```

CyberChef: **From Hex** · Terminal: `echo '<string>' | xxd -r -p`

**Flag 3 — A classic cipher (not a base-encoding)**

```
PGS{e0g4g3_gu3_4ycu4o3g}
```

The wrapper is right there but scrambled. Which cipher shifts letters by a fixed amount? · CyberChef: try **ROT13**.

**Flag 4 — Layered (the real challenge)**

```
NDM1NDQ2N2I3MDMzMzM2YzVmNzQ2ODMzNWY2YzM0NzkzMzcyNzM3ZA==
```

This one has **two layers**. Peel the outer encoding first — the result *looks* like a flag but isn't readable yet. Recon what you're left with, identify the inner layer, and peel again. (CyberChef's **Magic** wand can detect layers, but try to reason through it yourself first.)

> Submit each flag exactly as recovered, wrapper and all.

---

## Wrap-up & What's Next

**Recap:**

1. A CTF is a legal, gamified way to build real security skills by capturing flags.
2. Every challenge yields to the same workflow: recon → identify → exploit → recover → document.
3. The line between a security professional and a criminal is **authorization** — never forget it.

**Next session (Linux Basics & Command-Line Tools):** the terminal in depth — navigating the filesystem, finding and reading files, pipes and redirection, and the handful of commands you'll use in nearly every challenge from here on.

---

## Before the Next Session

1. **Set up your Linux environment** — pick one option from **Environment Setup** and get it running. Start the download tonight.
2. **Create accounts** (free) on at least one beginner platform:
  - [picoCTF](https://picoctf.org) — purpose-built challenges plus an always-on practice gym.
  - [TryHackMe](https://tryhackme.com) — guided, browser-based rooms.
  - [OverTheWire — *Bandit](https://overthewire.org/wargames/bandit/)* — the best free command-line warm-up; ideal pre-game for the next session.
3. **Stretch:** clear a few **OverTheWire Bandit** levels — they overlap directly with what's coming.

---

## Resources

- **[CTFtime.org](https://ctftime.org)** — calendar, rankings, team directory.
- **[picoCTF](https://picoctf.org)** — beginner challenges + a free learning gym.
- **[OverTheWire Wargames](https://overthewire.org/wargames/)** — *Bandit* (Linux), *Natas* (web).
- **[CyberChef](https://gchq.github.io/CyberChef/)** — encoding/decoding/crypto playground.
- **[National Cyber League (NCL)](https://nationalcyberleague.org)** — the competition these skills map to.

---

*CTF Training Series · Day 1 of 10*
