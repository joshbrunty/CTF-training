# Linux Basics and Command-Line Tools

**CTF Training Series**
**Level:** Intermediate-friendly — by now your Linux environment is up and running; today we make the terminal feel like home.

> Your reference for this session. The drills and the practice flag are meant to be solved yourself — answers aren't printed here on purpose. You'll need the Linux environment you set up earlier (Kali in a VM, WSL, or native). If you're not set up yet, get there first.

---

## Learning Objectives

By the end of this session, you will be able to:

1. Navigate the Linux filesystem with `pwd`, `ls`, and `cd`, using both absolute and relative paths.
2. Read and inspect files with `cat`, `less`, `head`, `tail`, `file`, `strings`, `stat`, and `wc`.
3. Search effectively with `grep` (inside files) and `find` (for files).
4. Combine tools with **pipes and redirection** to build one-line solutions.
5. Read file permissions and run programs safely (`ls -l`, `chmod +x`, `./prog`, `sudo`).
6. Use the core CTF text toolkit — `tr`, `cut`, `sort`, `uniq`, `base64`, `xxd` — inside a pipeline.

---

## Why Live in the Terminal

Almost every security tool runs on Linux, and the command line is where they live. It's faster than clicking, it scales to thousands of files at once, and — crucially — it's **scriptable**: anything you do by hand once, you can automate forever.

You don't need to memorize hundreds of commands. A dozen core tools, combined with pipes, will carry you through most CTF challenges. Today is about those building blocks and the habit of chaining them together.

---

## Getting Oriented

Three commands answer *where am I*, *what's here*, and *how do I move*:

```bash
pwd              # print working directory — where you are right now
ls -la           # list everything, including hidden dotfiles + permissions
cd /some/path    # change directory
cd ..            # up one level
cd ~             # home
cd -             # jump back to the previous directory
```

**Paths:** an **absolute** path starts at the root `/` (e.g. `/etc/passwd`). A **relative** path starts from where you currently are (e.g. `../notes/flag.txt`). `.` is the current directory; `..` is the parent.

> Tab-completion is your friend — start typing a name and press **Tab**. It finishes names for you and quietly prevents typos.

---

## Looking at Files

| Command | What it does |
|---|---|
| `cat file` | Dump a whole file to the screen (`tac` reverses line order) |
| `less file` | Scroll large files — `q` quits, `/text` searches, `G` jumps to end |
| `head -n 20 file` / `tail -n 20 file` | First / last lines (`tail -f` follows a file live) |
| `file thing` | Identify what a file *really* is, regardless of extension |
| `strings binary` | Pull human-readable text out of a non-text file |
| `stat file` | Size, timestamps, permissions |
| `wc -l file` | Count lines (also `-w` words, `-c` bytes) |

> CTF habit: when you get a mystery file, run `file` on it **first**, then `strings`. The extension lies; the bytes don't.

---

## Finding Things

Two different jobs: searching *inside* files vs. searching *for* files.

**`grep` — search inside files**

```bash
grep password file              # lines containing "password"
grep -r pattern dir/            # search a whole directory tree
grep -i pattern file            # case-insensitive
grep -n pattern file            # show line numbers
grep -RinE 'CTF\{.*\}' .        # hunt a flag across every file, recursively
```

**`find` — search for files**

```bash
find . -name '*.txt'            # by name / glob
find . -size +1M               # files larger than 1 MB
find . -type f -newer ref.txt  # by type, time, and more
which python3                   # where a command lives
locate bandit                   # fast name lookup (run updatedb if empty)
```

---

## Pipes & Redirection

The **pipe** (`|`) sends one command's output straight into the next. This is the single most important idea in the shell: small tools, chained into exactly the answer you need.

```bash
cmd1 | cmd2          # feed output of cmd1 into cmd2
command > file       # redirect output to a file (overwrites)
command >> file      # append instead of overwrite
command < file       # read input from a file
command 2> errs.txt  # send errors somewhere
command 2>&1         # merge errors into normal output
command | tee out    # write to a file AND the screen
```

Example — find the one interesting line in a huge file without scrolling:

```bash
cat huge.log | grep -i error | tail -n 5
```

---

## Permissions & Running Things

```bash
ls -l                 # the rwx columns: read / write / execute
chmod +x script.sh    # make a file runnable
./script.sh           # run something in the current directory
sudo command          # run as administrator — use with care
```

Permissions come in three sets — **owner**, **group**, **others** — each with the same three bits (`r`, `w`, `x`). Reading `ls -l` output is a routine CTF step.

> The safety rule still applies: **never run an unknown binary outside your VM.** Inspect it (`file`, `strings`, a disassembler) before you ever execute it.

---

## The CTF Text Toolkit

These turn raw output into the exact string you want — usually inside a pipe.

| Tool | Use it for |
|---|---|
| `tr` | Translate or delete characters — ROT ciphers, stripping junk |
| `cut` | Slice out columns / fields |
| `rev` | Reverse each line |
| `sort` \| `uniq -c` | Count and dedupe; surface the odd one out |
| `base64 -d` | Decode Base64 — now inside a pipeline |
| `xxd` / `xxd -r -p` | Bytes ↔ hex |
| `sed` | Stream find-and-replace |
| `awk` | Field-by-field processing when you need real logic |

---

## Putting It Together

Real solving is **chaining**. Say a file holds thousands of lines and one hides a flag:

```bash
grep -E 'CTF\{.*\}' notes.txt
```

One line — no scrolling, no guessing. The mindset is the same solve workflow: **recon** what you've got, **identify** the shape of the problem, then build a pipeline a stage at a time, checking the output of each stage before adding the next.

---

## Hands-on: Terminal Drills 🐧

Open your Linux environment and work through these. The goal is fluency with the moves, not speed.

1. **Navigate** — from your home folder, list all hidden files (`ls -la`), then `cd` into a directory and back with `cd -`.
2. **Inspect** — run `file` and then `strings` on any binary in `/bin`; see what readable text leaks out.
3. **Search** — use `grep -Rin password /etc` (read-only — just look) to see recursive search in action.
4. **Pipe** — run `ls /usr/bin | sort | tail` to show the last few program names alphabetically.

---

## Capture a Flag with One Pipeline 🚩

This Base64 string hides a flag in the form `CTF{...}`. Decode it with a **single pipeline** — no pasting into a website.

```
Q1RGe2dyM3BfcDFwM3NfcDB3M3J9
```

- Echo the string and pipe it into `base64 -d`.
- Submit the `CTF{...}` exactly as it comes out.
- **Stretch:** pull *just* the flag out with `... | grep -o 'CTF{.*}'`.

---

## Common Gotchas

- **Quoting & spaces** — wrap arguments containing spaces in `'single quotes'`; a filename with spaces needs quotes or a `\` before each space.
- **Hidden files** — `ls -a` reveals dotfiles (`.bash_history`, `.secret`); flags love to hide there.
- **Line endings** — Windows `\r` characters silently break matches. Strip them with `tr -d '\r'`.
- **Case sensitivity** — `File`, `file`, and `FILE` are three different things in Linux.

---

## Wrap-up & What's Next

**Recap:**
1. You can move around the filesystem and read or inspect any file.
2. You can search inside files with `grep` and for files with `find`.
3. You can chain tools with pipes — the core loop of nearly every CTF challenge from here on.

**Next session (Cryptography Essentials):** hashing, encoding vs. encryption revisited, and breaking classic ciphers — all of it powered by the pipelines you built today.

---

## Before the Next Session

1. **Drill on OverTheWire Bandit** — connect over SSH and clear **levels 0–10**. Each level hands you the login for the next once you solve it, and they map directly onto today's commands.
2. **Keep a command notes file** — for each Bandit level, jot the one command that cracked it. This becomes your personal cheat sheet.
3. **Stretch:** push as far past level 10 as you can before the next session.

---

## Resources

- **[OverTheWire — Bandit](https://overthewire.org/wargames/bandit/)** — the canonical command-line wargame.
- **[explainshell.com](https://explainshell.com)** — paste any command; it annotates every flag.
- **[The Linux Command Line (free book)](https://linuxcommand.org/tlcl.php)** — thorough, beginner-friendly reference.
- **[ss64.com](https://ss64.com/bash/)** — quick lookup for any Bash command and its options.
- **[CyberChef](https://gchq.github.io/CyberChef/)** — when you'd rather decode visually than in a pipe.

---

*CTF Training Series*
