## CTF Training

A growing collection of hands-on CTF training challenges and walkthroughs built for the Marshall University Cyber Team in preparation for the [National Cyber League (NCL)](https://nationalcyberleague.org), [US Cyber Games](https://www.uscybergames.com), and other competitions. Challenges are designed to build real, competition-ready skills — the same techniques you'll use on game day and on the job.

---

## Challenge Index

| # | Category | Challenge | Difficulty | Topics |
|---|---|---|---|---|
| RE-01 | Reverse Engineering | [Vault](./RE-01-vault/RE-01-vault.md) | Beginner | Static analysis, disassembly, Ghidra, XOR encoding |

*More challenges added regularly. Check back often.*

---

## Tools & Environment

Most challenges are designed for **Kali Linux** and make use of standard tools including:

- `file`, `strings`, `objdump` — CLI static analysis
- [Ghidra](https://ghidra-sre.org) — NSA reverse engineering framework
- `python3` — scripting and decoding
- `gdb` — dynamic analysis (future challenges)

> **Apple Silicon users:** Some challenges use x86-64 binaries. If `objdump` returns `can't disassemble for architecture UNKNOWN`, install the cross-platform binutils:
> ```bash
> sudo apt install binutils-x86-64-linux-gnu
> x86_64-linux-gnu-objdump -d <binary>
> ```

---

## Flag Format

All challenges in this repo use the format: **`MU{...}`**

---

## Repo Structure

```
CTF-training/
├── RE-01-vault/
│   ├── README.md            ← Challenge description & Reverse Engineering 101 overview
│   ├── vault                ← Challenge binary (ELF 64-bit x86-64)
│   └── MU_RE_Challenge_Vault.md   ← Student worksheet
└── ...
```

Instructor solution guides and source files are maintained in a separate private repository. If you are an instructor and need access, contact [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu).

---

## About This Series

These challenges were developed by **Dr. Josh Brunty**, Professor of Cyber Forensics & Security and Head Coach of the [US Cyber Team](https://www.uscyberteam.com) at [Marshall University](https://www.marshall.edu/cyber). They are intended to bridge the gap between classroom knowledge and competition performance — with a focus on techniques that apply equally in real-world digital forensics and incident response work.

---

## Contributing

Have a challenge idea or want to contribute a walkthrough? Open an issue or reach out directly.
