# Reverse Engineering Basics — Taking a Binary Apart

**CTF Training Series** · **Flag format:** `CTF{...}`
**Level:** Intermediate-friendly — this builds on the `file`/`strings` habits from the Linux session and the XOR idea from Crypto.

> This single README is both the **lesson** and the **lab**. Read the concepts in Part 1, then work the **Vault** challenge in Part 2. Its flag isn't printed here — that's the point. Work inside your Kali VM, and **never run an unknown binary outside a VM.**

---

## Learning Objectives

By the end of this session, you will be able to:

1. Explain what reverse engineering is and the difference between **static** and **dynamic** analysis.
2. Recon a binary with `file` and `strings`, and explain why encoded data won't show up in `strings`.
3. Read disassembly with `objdump` — locate functions, calls, and key constants.
4. Decompile with **Ghidra** and read the pseudocode despite stripped variable names.
5. Recognize **XOR encoding** as obfuscation and recover the hidden data.
6. Apply the RE workflow: **recon → disassemble → decompile → recover.**

---

# Part 1 — The Lesson

## What Is Reverse Engineering?

Reverse engineering (RE) is figuring out what a compiled program does **without** the source code. You're handed machine code and have to work backward to its logic.

- **Static analysis** — examining the binary *without running it* (`file`, `strings`, `objdump`, Ghidra). Safe, and where you start.
- **Dynamic analysis** — *running* it under observation (a debugger like `gdb`, or a sandbox). Powerful, but only ever inside an isolated VM.

> **Safety first:** a CTF binary is usually friendly, but the habit matters — analyze unknown files statically, in a VM, before you ever execute them. In the real world that binary could be malware.

---

## From Source to Binary

When a developer compiles a program, readable source becomes CPU instructions:

```
source code  →  [compiler]  →  machine code (the binary)
```

RE walks that backward — but you don't get the source back. Two things make it harder, and both are normal:

- **No comments or original names.** The compiler throws them away.
- **Stripped symbols.** Many binaries remove function and variable names entirely, so a decompiler invents generic ones like `param_1`, `local_28`, `FUN_004011d6`. Your job is to read the *logic*, not rely on names.

A binary that *keeps* a meaningful function name (say, `xor_decode`) is handing you a breadcrumb — follow it.

---

## The Three-Phase Approach

Most beginner RE challenges fall to the same escalating sequence. Use the cheapest tool that answers your question, and only escalate when you need to.

1. **Recon** — `file` and `strings`. What is this, and what's sitting in plain sight?
2. **Disassemble** — `objdump`. Read the raw assembly to find the interesting function and the constants it uses.
3. **Decompile** — Ghidra. Turn the assembly into C-like pseudocode you can actually reason about.

**Recon** tells you the architecture and surfaces embedded text — banners, prompts, and especially **function names**. If a flag *doesn't* appear in `strings`, that absence is itself a clue: it's probably encoded, because encoded bytes aren't printable.

**Disassembly** (`objdump -d`) lets you find `main`, spot a `call` to a non-library function (library calls end in `@plt`), and read the constant loaded just before that call — often a key, a length, or a flag.

**Decompilation** (Ghidra) turns machine code into readable pseudocode. Expect generic names; hunt for the operator that matters. A `^` is XOR, and a loop doing `*(out + i) = key ^ *(enc + i)` is a decode routine — the byte it XORs with is the key.

---

## Spotting Obfuscation: XOR

The most common "hidden flag" trick in beginner RE is exactly the XOR you met in the Crypto session — now living *inside a binary*.

- The flag is stored **XOR-encoded** so `strings` shows nothing readable.
- A small routine decodes it at runtime by XORing each byte with a **key**.
- That key is almost always **right there in the binary** — a constant loaded just before the decode call, visible in both `objdump` and Ghidra.

Recover the encoded bytes + the key, XOR them together (a five-line Python script), and you have the flag. XOR is its own inverse, so decoding is the same operation as encoding.

---

## The RE Workflow

1. **Recon** — `file` + `strings`; note what's present and what's suspiciously absent.
2. **Disassemble** — `objdump`; find the interesting function and the constants near its call.
3. **Decompile** — Ghidra; read the logic, identify the scheme (e.g. XOR + key).
4. **Recover** — pull the bytes and key, decode, and submit the `CTF{...}` flag.
5. **Note** — record the breadcrumb that cracked it; that's your write-up.

---

## Your Toolkit

| Tool | Purpose |
|---|---|
| `file` | Identify type, architecture, stripped-or-not |
| `strings` | Printable text, function names, breadcrumbs |
| `objdump -d` | Disassembly; find calls and constants |
| **Ghidra** | Full decompilation to readable pseudocode |
| `gdb` | Dynamic analysis when static isn't enough (a later topic) |
| `python3` | A few lines to XOR-decode recovered bytes |

> On Kali, install Ghidra with `sudo apt install ghidra -y`. See the ARM-Mac note in Phase 2 if `objdump` won't read the binary.

---

# Part 2 — The Lab: RE-01 Vault

**Category:** Reverse Engineering · **Difficulty:** Beginner · **Flag Format:** `CTF{...}`

## Challenge Description

You've intercepted a suspicious binary from a threat actor's server. The binary appears to be some kind of vault access system — but the password is nowhere to be found in any documentation, email, or file system.

Your mission: **reverse engineer the binary and recover the hidden flag.**

The binary is named `vault`. It runs on Linux (64-bit).

> *"The answer is already inside..."*

## Files Provided

| File | Description |
|---|---|
| [`vault`](./vault) | The challenge binary (ELF 64-bit, Linux x86-64) |

> After downloading, make it runnable: `chmod +x vault`. (You don't actually need to *run* it to solve it — static analysis first.)

---

## Roadmap

Work through these phases in order. **Do not skip ahead** — each phase builds intuition you'll need for the next.

### Phase 1 — Reconnaissance (`file` + `strings`)

**Goal:** Understand what you're working with and gather initial intel.

1. Run `file vault` and record what you observe.
   - What type of binary is this? What architecture is it compiled for?
2. Run `strings vault` and scroll through the output.
   - Any interesting messages? Does the flag appear directly? Why or why not?
   - What function names or library calls can you spot?
3. Run `strings vault | grep -i "flag\|CTF{\|password\|secret"` — what do you find?

   You should see something like:
   ```
   [-] Access Denied. Wrong password.
   Enter the vault password:
   [+] Flag: %s
   ```
   On Kali, `grep` highlights the matched terms in red — that's normal. Notice that `CTF{` returns **no match**. What does that tell you about the flag?

**Answer these before moving on:**
- [ ] What is the binary type and architecture?
- [ ] Is the flag visible in plaintext? (Yes / No)
- [ ] What hint message is embedded in the binary?
- [ ] What suspicious function name(s) do you see in `strings` output?

### Phase 2 — Disassembly (`objdump`)

**Goal:** Inspect the raw assembly to understand program flow.

> **ARM Kali users (Apple Silicon Mac):** If `objdump -d vault` returns `can't disassemble for architecture UNKNOWN`, your Kali is running on ARM64 and the default `objdump` can't read x86-64 binaries. Fix it:
> ```bash
> sudo apt install binutils-x86-64-linux-gnu
> x86_64-linux-gnu-objdump -d vault
> ```
> Use `x86_64-linux-gnu-objdump` in place of `objdump` for the rest of Phase 2. Ghidra (Phase 3) is unaffected.

1. Run `objdump -d vault` to disassemble all functions.
   - Locate `main`. Locate any other interesting function — what does the name suggest?
2. Read the assembly:
   - What library functions are called? (Look for `call` instructions referencing `@plt`.)
   - Find the instruction that loads a single-byte constant into a register **just before** calling the suspicious function. What is that value (in hex)?
3. Run `objdump -s -j .rodata vault` to inspect the read-only data section.
   - What strings are there? Any sequences of non-printable bytes? Note them.

**Answer these before moving on:**
- [ ] What is the name of the suspicious non-standard function you found?
- [ ] What hex value is loaded just before that function is called?
- [ ] Can you identify what that function might be doing based on its name?

### Phase 3 — Deep Analysis (Ghidra)

**Goal:** Decompile the binary and fully understand the flag-hiding mechanism.

**Setup:**
1. Install if needed: `sudo apt update && sudo apt install ghidra -y`
2. New Project → **Import File** → select `vault` → accept defaults.
3. Double-click `vault` to open the Code Browser → **Analyze** → Yes → accept defaults.

**Tasks:**
1. **Symbol Tree → Functions** → open `main` and `xor_decode` in the **Decompiler** panel.
2. About `xor_decode`:
   - How many parameters does it take? What operation does it perform on each byte (look for `^`)?
   - What are the parameters named in Ghidra?

   > **Note:** Without debug symbols, Ghidra uses generic names (`param_1`…`param_4`) instead of `out`, `enc`, `len`, `key`. That's normal RE — figure out what each parameter *does*. Trace the XOR: which parameter is written to? read from? which is the single-byte constant?

3. In `main`, find the call to `xor_decode`. It looks like:
   ```
   xor_decode(local_68, &local_28, 0x1a, 0x42);
   ```
   - What is the fourth argument? That's your key.
   - The encoded bytes are stored as large integer chunks across several stack variables (`local_28`, `uStack_1f`, …) just above the call. Rather than parse those by hand, recover the raw bytes from the `movabs` instructions in your Phase 2 `objdump` output.
4. XOR each encoded byte with the key. The result is your flag.

<details>
<summary>Decode script (open only after you've found the key and bytes yourself)</summary>

Create `decode.py`:
```python
enc = [0x01,0x16,0x04,0x39,0x25,0x2a,0x73,0x26,
       0x30,0x23,0x1d,0x73,0x31,0x1d,0x3b,0x72,
       0x37,0x30,0x1d,0x24,0x30,0x73,0x71,0x2c,
       0x26,0x3f]
key = 0x42
print(''.join(chr(b ^ key) for b in enc))
```
Run it: `python3 decode.py`

</details>

---

## Submission

Enter the flag (include the full `CTF{...}` format):

**Flag:** `CTF{_______________________}`

---

## Hints (Use Only If Stuck!)

> Reveal one at a time. Each hint costs you 10 points.

<details>
<summary>Hint 1 — What does <code>strings</code> reveal?</summary>

Look at the function names in `strings vault`. One isn't a standard C library function. What could a function called `xor_decode` be doing?

</details>

<details>
<summary>Hint 2 — Finding the key in objdump</summary>

In `objdump -d vault`, search for the call to `xor_decode`. Right before it, a `mov` instruction loads a value into `%ecx`. That value is your XOR key.

</details>

<details>
<summary>Hint 3 — Decoding in Ghidra</summary>

In the decompiler view of `xor_decode`, the `^` operator is XOR. `param_4` is the key; `param_2` is the encoded byte array. In `main`, the call shows the key as the fourth argument. Collect the encoded bytes, XOR each with the key, convert to ASCII.

For each encoded byte, XOR with the key to get its ASCII value:

```
Encoded   ^   Key    =   ASCII (hex)   =   Character
----------------------------------------------------
0x01      ^   0x42   =   0x43          =   'C'
0x16      ^   0x42   =   0x54          =   'T'
0x04      ^   0x42   =   0x46          =   'F'
```

Continue for all 26 bytes; the characters concatenated form the flag.

</details>

<details>
<summary>Hint 4 — The XOR math explained</summary>

The encoded bytes were made by XORing each flag character's ASCII value with key `0x42`:

```
'C' = 0x43 ^ 0x42 = 0x01
'T' = 0x54 ^ 0x42 = 0x16
'F' = 0x46 ^ 0x42 = 0x04
```

To decode, XOR each encoded byte with the same key — XOR is its own inverse, so `C ^ B = A` whenever `A ^ B = C`.

</details>

---

## Key Concepts Practiced

- **Static analysis** — examining a binary without running it
- **ELF binary structure** — file type, architecture, sections
- **Strings extraction** — finding embedded data in binaries
- **Disassembly** — reading x86-64 assembly
- **XOR encoding** — a common obfuscation technique in CTF and malware
- **Decompilation with Ghidra** — recovering high-level logic from machine code

---

## Common Pitfalls

- **Running the binary first.** Static analysis before execution — always, and in a VM.
- **Giving up when `strings` shows no flag.** That absence *is* the clue — something is encoding it.
- **Fighting Ghidra's generic names.** `param_1`/`local_28` are normal on stripped binaries. Read the logic; rename variables as you identify them.
- **Mixing up the key and the data.** In the decode loop, the byte every iteration XORs against is the key; the buffer it walks through is the data.
- **Endianness by hand.** Bytes inside `movabs`/stack stores are little-endian (reversed). Let Ghidra normalize them, or reverse carefully.

---

## Wrap-up & What's Next

**Recap:**
1. RE is understanding a binary without its source — start static, stay in a VM.
2. Escalate through the tools: `file`/`strings` → `objdump` → Ghidra.
3. A kept function name is a breadcrumb; XOR-with-a-nearby-key is the classic beginner trick.

**Next session (Digital Forensics):** recovering hidden and deleted data from files, disks, and metadata.

**Before then:**
1. Practice RE on [picoCTF](https://picoctf.org) — the Reverse Engineering track starts gentle and ramps up.
2. Try [crackmes.one](https://crackmes.one) — a huge library of small "crack me" binaries; filter to beginner.
3. **Stretch:** re-open the Vault in Ghidra and rename variables as you identify them.

---

## Resources

- **[Ghidra](https://ghidra-sre.org)** — the free decompiler used in this session.
- **[picoCTF](https://picoctf.org)** — beginner-friendly Reverse Engineering practice.
- **[crackmes.one](https://crackmes.one)** — graded reverse-engineering practice binaries.
- **[Compiler Explorer (godbolt.org)](https://godbolt.org)** — see how C source maps to assembly, in reverse.

---

*Prepared by Coach Josh Brunty*
*Contact: [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu) | [coachbrunty@uscybergames.org](mailto:coachbrunty@uscybergames.org)*
*CTF Training Series*
