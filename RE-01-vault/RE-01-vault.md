# MU Cyber Team — CTF Challenge
## *Reverse Engineering | "Vault"*
**Difficulty:** Beginner  
**Category:** Reverse Engineering  
**Flag Format:** `MU{...}`

---

## Challenge Description

You've intercepted a suspicious binary from a threat actor's server. The binary appears to be some kind of vault access system — but the password is nowhere to be found in any documentation, email, or file system.

Your mission: **reverse engineer the binary and recover the hidden flag.**

The binary is named `vault`. It runs on Linux (64-bit).

> *"The answer is already inside..."*

---

## Files Provided

| File | Description |
|---|---|
| [`vault`](./vault) | The challenge binary (ELF 64-bit, Linux x86-64) |

---

## Recommended Tools

You have access to the following tools on Kali Linux:

| Tool | Purpose |
|---|---|
| `file` | Identify the binary type and architecture |
| `strings` | Extract printable strings from the binary |
| `objdump` | Disassemble the binary and inspect sections |
| **Ghidra** | Full decompilation and static analysis |

---

## Challenge Roadmap

Work through these phases in order. **Do not skip ahead** — each phase builds intuition you'll need for the next.

---

### Phase 1 — Reconnaissance (`file` + `strings`)

**Goal:** Understand what you're working with and gather initial intel.

**Tasks:**

1. Run `file vault` and record what you observe.
   - What type of binary is this?
   - What architecture is it compiled for?

2. Run `strings vault` and scroll through the output.
   - Do you see any interesting messages?
   - Does the flag appear directly? Why or why not?
   - What function names or library calls can you spot?

3. Run `strings vault | grep -i "flag\|MU{\|password\|secret"` — what do you find?

**Answer these before moving on:**
- [ ] What is the binary type and architecture?
- [ ] Is the flag visible in plaintext? (Yes / No)
- [ ] What hint message is embedded in the binary?
- [ ] What suspicious function name(s) do you see in `strings` output?

---

### Phase 2 — Disassembly (`objdump`)

**Goal:** Inspect the raw assembly to understand program flow.

> **ARM Kali users (Apple Silicon Mac):** If `objdump -d vault` returns `can't disassemble for architecture UNKNOWN`, your Kali install is running on ARM64 and the default `objdump` can't read x86-64 binaries. Fix it with one command:
> ```bash
> sudo apt install binutils-x86-64-linux-gnu
> x86_64-linux-gnu-objdump -d vault
> ```
> Use `x86_64-linux-gnu-objdump` in place of `objdump` for the rest of Phase 2. Ghidra (Phase 3) is unaffected — it handles x86-64 on any host platform.

**Tasks:**

1. Run `objdump -d vault` to disassemble all functions.
   - Locate the `main` function.
   - Locate any other interesting function(s) — what does the name suggest?

2. Look at the assembly carefully:
   - What library functions are called? (Hint: look for `call` instructions referencing `@plt`)
   - Find the instruction that loads a single-byte constant into a register just before calling the suspicious function. What is that value (in hex)?

3. Run `objdump -s -j .rodata vault` to inspect the read-only data section.
   - What strings do you find there?
   - Do you see any sequences of non-printable bytes? Note them down.

**Answer these before moving on:**
- [ ] What is the name of the suspicious non-standard function you found?
- [ ] What hex value is loaded just before that function is called?
- [ ] Can you identify what that function might be doing based on its name?

---

### Phase 3 — Deep Analysis (Ghidra)

**Goal:** Decompile the binary and fully understand the flag-hiding mechanism.

**Setup:**
1. Open Ghidra and create a new project.
2. Import `vault` (`File → Import File`). Accept defaults and click OK.
3. Double-click `vault` in the project window to open the Code Browser.
4. When prompted to analyze, click **Yes** and accept defaults. Wait for analysis to complete.

**Tasks:**

1. Open the **Symbol Tree** panel (left side). Expand `Functions`.
   - Navigate to `main`. Read the decompiled C code in the **Decompiler** panel.
   - Navigate to `xor_decode`. Read its decompiled code.

2. Answer these questions about `xor_decode`:
   - How many parameters does it take?
   - What operation does it perform on each byte? (Look for `^` in the decompiler output.)
   - What are the parameters named in Ghidra?

   > **Note:** Because the binary was compiled without full debug symbols, Ghidra won't know the original variable names. Instead of `out`, `enc`, `len`, and `key`, you'll see generic names like `param_1`, `param_2`, `param_3`, and `param_4`. This is completely normal in real-world RE — your job is to figure out what each parameter *does* by reading the logic. Trace the XOR operation: which parameter is being written to? Which is being read from? Which is the single-byte constant?

3. Back in `main`, find the call to `xor_decode`. It will look like this:
   ```
   xor_decode(local_68, &local_28, 0x19, 0x42);
   ```
   - What is the fourth argument? That's your key.
   - Notice that the encoded bytes are **not** a clean array — they're stored as large integer chunks across several stack variables (`local_28`, `uStack_1f`, `uStack_18`, etc.) right above the `xor_decode` call. This is normal. Rather than manually parsing those integers, use your `objdump -d vault` output from Phase 2 to recover the raw encoded bytes — they're in the `movabs` instructions just before the `xor_decode` call.

4. Once you have the encoded bytes and the key:
   - XOR each byte with the key.
   - The result is your flag.

**You can decode the bytes using Python in your terminal:**
```python
python3 -c "
enc = [/* paste your hex bytes here, comma-separated */]
key = 0x??  # fill in the key you found from Ghidra
print(''.join(chr(b ^ key) for b in enc))
"
```

---

## Submission

Once you've decoded the flag, enter it below (include the full `MU{...}` format):

**Flag:** `MU{_______________________}`

---

## Hints (Use Only If Stuck!)

> Reveal one at a time. Each hint costs you 10 points.

<details>
<summary>Hint 1 — What does <code>strings</code> reveal?</summary>

Look at the function names visible in `strings vault`. One of them is not a standard C library function. What could a function called `xor_decode` possibly be doing?

</details>

<details>
<summary>Hint 2 — Finding the key in objdump</summary>

In `objdump -d vault`, search for the call to `xor_decode`. Right before the call, there's a `mov` instruction loading a value into `%ecx`. That value is your XOR key.

</details>

<details>
<summary>Hint 3 — Decoding in Ghidra</summary>

In Ghidra's decompiler view of `xor_decode`, look for the `^` operator — that's XOR. The fourth parameter (`param_4`) is the key, and the second (`param_2`) is the encoded byte array. In `main`, find the call to `xor_decode` and look at what's passed as `param_4` — that's your key. Collect the encoded bytes, XOR each one with the key, and convert to ASCII.

</details>

---

## Key Concepts Practiced

- **Static analysis** — examining a binary without running it
- **ELF binary structure** — understanding file type, architecture, sections
- **Strings extraction** — finding embedded data in binaries
- **Disassembly** — reading x86-64 assembly language
- **XOR encoding** — a simple but common obfuscation technique in CTF and malware
- **Decompilation with Ghidra** — recovering high-level logic from machine code

---

*Marshall University Cyber Team | Reverse Engineering Training Series*  
*Prepared by Coach Josh Brunty
