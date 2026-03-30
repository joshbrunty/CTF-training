 RE-01: Vault
**Category:** Reverse Engineering | **Difficulty:** Beginner | **Points:** 100

## Reverse Engineering 101

A beginner-level reverse engineering challenge built for CTF prep. Analyze a mystery binary using static analysis tools and Ghidra, uncover hidden and encoded flags, and walk away knowing exactly how to approach RE challenges both in competition and on-the-job.

---

## Files

| File | Description |
|---|---|
| `vault` | Challenge binary (ELF 64-bit, x86-64, Linux) |
| `MU_RE_Challenge_Vault.md` | Student worksheet — work through this |

---

## Tools

`file` · `strings` · `objdump` · `Ghidra` · `python3`

> **Apple Silicon / ARM Kali users:** If `objdump -d vault` returns `can't disassemble for architecture UNKNOWN`, run:
> ```bash
> sudo apt install binutils-x86-64-linux-gnu
> x86_64-linux-gnu-objdump -d vault
> ```

---

## Flag Format

`MU{...}`

---

## Skills Covered

- Identifying binary type and architecture with `file`
- Extracting embedded strings with `strings`
- Reading x86-64 disassembly with `objdump`
- Decompiling and analyzing binaries in Ghidra
- Understanding and reversing XOR encoding

---

*Instructor solution guide available upon request — contact [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu)*

*Marshall University Cyber Team | Prepared by Coach Josh Brunty*
