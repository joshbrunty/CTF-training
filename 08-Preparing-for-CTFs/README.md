# Preparing for and competing in CTFs — Formats, Scoring, Teams & AI

**CTF Training Series**
**Level:** All levels — this is the capstone. There's no new tool to learn here; the skill is turning seven modules' worth of ability into points on a board, alone or as a team, under a clock.

> Modules 1–7 taught you how to *solve*. This one is about how to *compete* — choosing the right events, reading the scoring model, building a team that covers the board, using AI without outsourcing your brain, and staying sharp in hour six.

---

## Learning Objectives

By the end of this session, you will be able to:

1. Distinguish the major CTF **formats** (jeopardy, attack–defense, king-of-the-hill) and what each rewards.
2. Explain how **individual** and **team** competitions differ, and adjust your preparation for each.
3. Compare **in-person** and **virtual** events and prepare for the practical differences.
4. Identify the common **scoring models** — static, decay, first-blood, unlock chains, penalties — and build a strategy for each.
5. **Triage** a scoreboard by value, effort, decay, and your own strengths.
6. Apply **AI tools** — both conversational and agentic — legally, ethically, and effectively, and recognize where they fail.
7. Compose a **balanced team** across categories and experience levels, with no single point of failure.
8. Run the collaboration, flag, and time-management habits that separate a team from a crowd.

---

# Part 1 — The Lesson

## From Solving to Competing

You can already work crypto, web, packets, forensics, and binaries. Competing well is a *different* skill set layered on top:

- **Prioritization** — which challenge, in which order, with whom.
- **Coordination** — a team that doesn't step on itself beats a team of stronger soloists.
- **Stamina** — a CTF is a marathon; the team that's still organized in hour six wins.

These meta-skills are learnable and they compound. They are also the part most players never deliberately practice, which is exactly why practicing them is worth a whole module.

---

## Competition Formats

Not every CTF is a board of puzzles. Know which kind you've entered before you plan.

| Format | How it works | What it rewards |
|---|---|---|
| **Jeopardy** | A board of challenges by category, each worth points. Submit a flag, get the points. | Breadth, triage, speed. Most collegiate and online events — NCL, picoCTF, most of CTFtime. |
| **Attack–Defense** | Each team runs identical vulnerable services. You patch yours while exploiting everyone else's. Points accrue per tick for stolen flags *and* for keeping your own services up. | Infrastructure skill, exploit development, automation, sysadmin discipline. |
| **King of the Hill** | Teams compete to compromise and hold a shared target. Points accrue per tick while you hold it. | Speed to foothold, persistence, and denying others. |
| **Boot2root / Range** | Compromise a machine or chain of machines end-to-end. Often untimed or loosely timed. | Depth over breadth; methodology. |

Everything else in this module assumes **jeopardy** unless stated, because that's what you'll meet first — but the habits transfer.

---

## Individual vs. Team Competitions

Many series run both. **[NCL](https://nationalcyberleague.org)**, for example, runs an Individual Game and then a Team Game on the same engine — the challenges look similar, but the games are not the same game.

| | **Individual** | **Team** |
|---|---|---|
| **Coverage** | Every category is yours. Weak spots show up immediately. | Specialists cover categories; you can go deep in one. |
| **Pacing** | You set it. No waiting, no handoffs. | Depends on coordination; idle time is a real cost. |
| **Failure mode** | Tunneling with nobody to pull you out. | Three people quietly solving the same challenge. |
| **What wins** | Breadth + personal triage discipline. | Communication + division of labor. |
| **How to prepare** | Grind your *weakest* category. The easy flags in every area are worth more than a fourth hard flag in your specialty. | Grind your *strongest* category and practice handoffs, notes, and claiming. |

**The practical upshot:** train breadth for the individual game, depth plus coordination for the team game. A player who only ever competes on a team can hide a blind spot for years — the individual game finds it in twenty minutes.

---

## In-Person vs. Virtual

The challenges may be identical. The experience is not.

**In-person**
- Communication is nearly free — you turn your head and ask. Use it: talk constantly, share screens, walk to the whiteboard.
- Verify your environment *before* you sit down. Power, adapters, monitor cables, and a working VM you've booted at least once on that machine.
- Assume the venue network is hostile or slow. Pre-download tooling, wordlists (`rockyou.txt`), VM images, and offline documentation.
- Read the house rules: some events restrict internet access, external services, or which machines you may use.
- Fatigue is physical. Water, food, and standing up matter more than you think.

**Virtual**
- Communication has to be deliberate. Pick a voice channel and *stay in it* — text-only teams drift apart.
- Distraction is the enemy. Treat it like a scheduled event: same room, same hours, notifications off.
- Multi-day online events cross time zones. Plan shifts and hand off in writing, not in someone's head.
- You control your setup completely — so there's no excuse for a broken one. Test your VM, your notes doc, and your connection the day before.
- Latency to the scoreboard is real. On decay scoring, a flaky connection costs points.

---

## Scoring Models — and How to Play Each One

**Read the scoring rules before you read the board.** They change what "the best challenge to work on" even means.

### Static (fixed) scoring
Every challenge is worth a fixed number of points, announced up front. Simple and predictable.

- **Strategy:** pure point-to-effort ratio. Sort by value, filter by what your team can actually close, work top-down.
- **Watch for:** the 500-point challenge nobody solves. Fixed points are a *promise* of value, not evidence anyone can reach it.

### Dynamic (decay) scoring
A challenge starts high and its value *drops* as more teams solve it — often on a curve from, say, 500 down to a floor of 50. Everyone who solved it is retroactively adjusted to the current value.

- **Strategy:** early solves on soon-to-decay challenges are worth the most. Get on the board fast, then move to challenges that *won't* decay — the hard ones few teams crack hold their value.
- **The trap:** grinding easy challenges late. That "300-point" web challenge is worth 60 by hour four because everyone got it.
- **Re-survey the board** every 30–60 minutes; the map changes under you.

### Time-based decay
Value drops with elapsed event time rather than solve count.

- **Strategy:** front-load aggressively. Your first two hours are worth more than your last four.

### First blood
A bonus (points, or just glory) for the first team to solve a challenge.

- **Strategy:** send your specialist at their category the moment the board opens. If your crypto lead can take three crypto challenges in the first twenty minutes, that's where the bonus lives.
- Don't chase first blood outside your strengths — you'll lose more in wasted time than the bonus is worth.

### Unlock chains / prerequisite trees
Challenges hidden until you solve the one before them. Common in narrative or forensics-heavy events.

- **Strategy:** a locked branch is *invisible* value. Solving a cheap gate challenge may open three expensive ones. Prioritize gates early even when they look low-value.

### Wrong-answer penalties and hint costs
Some platforms deduct points for incorrect submissions, or charge points to unlock a hint.

- **Strategy:** verify the flag format before submitting; don't spray guesses. Budget hint points deliberately — a hint that unblocks 45 minutes is usually worth 10% of the challenge.

### Tie-breaks
When two teams finish level, the one that reached the score **first** almost always wins.

- **Strategy:** steady early submissions beat a late flurry at identical totals. Submit the moment you have a flag; don't sit on it.

### Attack–defense scoring
Points come per tick from two streams: flags captured from opponents, and your own **service uptime (SLA)**. A broken patch that takes your service down can cost more than the exploit it blocked.

- **Strategy:** patch conservatively, test before deploying, and automate flag submission. Defense is half the score.

### Quick reference

| Scoring model | What it rewards | Your play |
|---|---|---|
| Static | Raw solving power | Sort by point-to-effort, work top-down |
| Solve-count decay | Speed on popular challenges; depth on rare ones | Early cheap solves, then pivot to hard/unsolved |
| Time decay | Fast starts | Front-load; treat hour one as double |
| First blood | Specialist speed | Specialists hit their category at minute zero |
| Unlock chains | Exploration | Clear gates early, even cheap ones |
| Penalties / hint costs | Discipline | Verify format; budget hints as a resource |
| Tie-break by time | Consistency | Submit immediately, never bank flags |
| Attack–defense (SLA) | Uptime + offense | Patch carefully; never trade uptime for a fix |

---

## Read the Board, Then Triage

Don't start at the top-left and grind down. **Survey first, then choose.**

- **Point-to-effort ratio.** Grab cheap, fast flags early to get on the board and build momentum — but don't *only* eat easy points if higher-value challenges match your strengths.
- **Play to strengths first.** Send each person at their best category before anyone touches something they'll struggle with.
- **Check solve counts.** A challenge with 200 solves is probably approachable and probably decayed. One with 2 solves is either brutal or broken.
- **Don't tunnel.** If a challenge has eaten 45 minutes with no progress, mark it, write down where you're stuck, and move on. Come back with fresh eyes.
- **Re-survey periodically.** Decay, new solves, and unlocks change what's worth doing.

---

## AI in CTFs

AI is now part of the competitive landscape. Treating it as either a magic answer machine or a forbidden crutch will both cost you points.

### Rule zero: read the event's AI policy

Policies vary and they are not optional. You will encounter all of these:

- **Fully permitted** — use anything, no disclosure.
- **Permitted with disclosure** — allowed, but you must report what you used.
- **Restricted division** — separate scoreboards or brackets for AI-assisted and unassisted play.
- **Prohibited** — no external model access; sometimes enforced by an air-gapped network.
- **Data-restricted** — you may use AI, but you may *not* upload challenge files or flags to a third-party service.

Assume nothing. Find the policy in the rules document before the clock starts, and if it's ambiguous, ask the organizers in writing. Violating an AI policy is a disqualification, not a warning.

### Non-agentic AI — the conversational assistant

A chat model with no ability to run your tools. You describe; it suggests. Where it genuinely earns its place:

- **Identification and hypothesis generation** — "this ciphertext is all uppercase, length 87, no digits" gets you a ranked list of candidates faster than your memory does.
- **Throwaway scripts** — a XOR brute-forcer, a pcap field extractor, a regex you'd otherwise spend fifteen minutes debugging.
- **Explaining unfamiliar things** — a file format, a protocol field, an assembly idiom, a library function's behavior.
- **Pseudocode from disassembly** — pasting a short function and asking what it does is a real accelerant in reverse engineering.
- **Rubber-ducking** — writing out where you're stuck often solves it, and the model answers back.

Where it fails, predictably:

- **It cannot see the artifact.** It's reasoning about your *description* of the binary, not the binary. Your description is where the bug usually is.
- **Confident wrongness in crypto.** Plausible, fluent, and wrong is the default failure mode on anything mathematical.
- **Invented flag formats.** Never submit a flag a model produced without confirming it came out of the actual challenge.
- **Attractive dead ends.** A well-written wrong hypothesis costs more time than no hypothesis, because it's *convincing*.

### Agentic AI — the assistant that runs tools

An agent that can execute commands, read files, and iterate on results. Different strengths, different risks:

**Where it helps**
- **Tireless enumeration** — running `strings`/`file`/`binwalk`/`exiftool` across dozens of artifacts and reporting what stands out.
- **Mechanical breadth** — trying every classical cipher, every single-byte XOR key, every common encoding, in parallel with you thinking.
- **First-pass triage** — a quick characterization of a pcap or disk image so you know which challenge is worth your hour.
- **Scaffolding** — writing, running, and debugging a solver script in a loop instead of you round-tripping through copy-paste.

**Where it hurts**
- **Loops burn the clock.** An agent can spend twenty minutes confidently going nowhere. Time-box it exactly like a human teammate.
- **Evidence integrity.** In forensics, an agent that writes to your disk image has destroyed your artifact. Work on copies; mount read-only.
- **It doesn't have the insight.** The "aha" — noticing that Module 3's encoding just showed up inside Module 7's binary — is still yours.
- **Sandbox and data risk.** Running untrusted challenge binaries under an agent, on a machine with your notes and credentials, is a bad trade. Isolate.
- **Silent policy violations.** An agent that reaches the internet may upload challenge data. Know what it's sending.

### Using AI well

1. **AI on the known, humans on the novel.** Delegate the mechanical and the boilerplate. Keep the leaps.
2. **Verify against the artifact, always.** Every claim gets checked against the real file, the real capture, the real output.
3. **A human submits.** Never let an automated pipeline submit flags unreviewed — wrong-answer penalties and format errors are avoidable.
4. **Time-box it like any other lead.** Fifteen minutes with no progress means try something else, agent or not.
5. **Isolate.** Copies of evidence, a disposable VM, no credentials in the working directory.
6. **Have an offline fallback.** Air-gapped venues and blocked networks happen. Know how to work without it.
7. **Log what you used** if the event requires disclosure. Write it down as you go, not from memory afterward.

### The training caveat

During *practice* — including this course — solve it yourself first, then compare with what a model suggests. You cannot develop intuition you outsourced. The competitors who benefit most from AI are the ones who already know what a correct answer looks like, because they're the only ones who can tell when it's wrong.

---

## Building a Balanced Team

### Cover the board

A team is only as broad as its categories. Map yours honestly, and give every area a **primary and a backup** — one person out sick or stuck should never mean a category goes dark.

| Category | What it needs | Primary | Backup |
|---|---|---|---|
| **Cryptography** | Hashing, encodings, classical ciphers, XOR, some math patience | | |
| **Web Exploitation** | HTTP fluency, SQLi/XSS, DevTools, Burp, tokens | | |
| **Forensics** | File systems, carving, metadata, Autopsy / Sleuth Kit | | |
| **Network / Deep Packet Analysis** | Wireshark, `tshark`, protocol knowledge, stream reassembly | | |
| **Reverse Engineering** | `objdump`, Ghidra, assembly reading, patience | | |
| **Binary Exploitation (Pwn)** | Memory corruption, `gdb`/pwntools, ROP — the steepest curve | | |
| **OSINT** | Search craft, image/geo analysis, records, social footprints | | |
| **Scripting / Automation** | Python and shell — the cross-cutting skill *everyone* needs | | |

Pwn and RE are usually a team's thinnest coverage, and OSINT is the one most often left unassigned even though it's frequently the cheapest points on the board. Fill those deliberately.

### Balance experience levels

A mixed-experience roster is a feature, not a problem — if you use it on purpose.

- **Veterans / category leads** own their area and mentor. Roughly a quarter to a third of the team.
- **Solid mid-tier** players are the engine: competent in two or three categories, able to float.
- **Newcomers** take the on-ramp challenges — recon, metadata, encoding, easy OSINT. Those are *real points*, not busywork, and they build confidence fast.

Guidance that holds up:

- **Pair, don't isolate.** Put a newer member *with* a specialist on a challenge. They learn the workflow live; the specialist gets a second set of eyes and someone to explain to, which sharpens their own thinking.
- **Rotate.** Over a long event, let newer members lead a challenge with a specialist backstopping. That's how this year's beginners become next year's leads.
- **No single point of failure.** If exactly one person can do RE, your RE score is a coin flip on their day.

### Functional roles

Separate from category expertise, a few lightweight roles keep the team coherent:

- **Captain / coordinator** — watches the board, assigns and re-assigns, tracks time. Doesn't have to be the strongest solver; has to be the most organized.
- **Scribe** — owns the shared notes: what's solved, who's on what, where people are stuck.
- **Submitter** — one person submits, so format errors and penalty costs stay controlled.
- **Floaters** — strong generalists who jump to whoever needs a second pair of eyes.

---

## Collaboration Mechanics

The difference between a team and a crowd is a few simple habits:

- **One shared notes doc.** Every challenge gets a line: name, category, who's on it, status (untouched / in-progress / stuck / solved), and the flag once found.
- **Claim before you start.** "I've got Crypto 3" prevents two people quietly solving the same thing.
- **Write down where you're stuck** before you walk away — so the next person doesn't restart from zero.
- **Flag discipline.** Confirm the exact format before submitting; submit the moment you have it; paste the flag into the notes so the team sees the win and nobody re-solves it.
- **Hand off cleanly.** "Here's what I tried, here's the output, here's my hunch" turns a stuck challenge into a fresh start for someone else.
- **Share output, not just conclusions.** Paste the actual `objdump` / `exiftool` / stream output — a second person spots what the first glossed over.

---

## Time & Energy Management

Endurance is strategy:

- **Timebox.** Cap how long anyone sinks into one challenge before they surface and re-assess.
- **Take real breaks.** Eat, walk, sleep on multi-day events. Tired solvers miss obvious things.
- **Watch the clock as a team.** Know how much time is left and what's realistically closable.
- **The last hour.** Stop starting *new* hard challenges; consolidate near-finished ones, double-check submitted flags, and grab any remaining cheap points.
- **Protect morale.** Celebrate every flag out loud. A team that feels momentum keeps finding flags.

---

## Common Failure Modes

| Anti-pattern | The fix |
|---|---|
| Everyone piles onto one shiny challenge | Spread across categories; one or two per challenge |
| Lone-wolfing — no one knows who's on what | Claim challenges in the shared notes |
| No notes, so handoffs restart from zero | Scribe + "where I'm stuck" lines |
| Grinding easy challenges late under decay scoring | Re-survey the board hourly; pivot to non-decayed value |
| Ignoring locked branches | Clear cheap gate challenges early |
| Spraying guesses under a penalty scoring model | Confirm flag format; one submitter |
| Wrong flag format / trailing newline | Copy-paste exactly, wrapper and all |
| Tunneling for hours on one problem | Timebox and re-survey |
| Trusting an AI answer without checking the artifact | Verify every claim against the real file |
| Letting an agent loop unsupervised | Time-box the agent like a teammate |
| Uploading challenge data against event rules | Read the AI policy before the clock starts |
| Burnout by hour six | Breaks, rotation, celebrate wins |

---

# Part 2 — The Lab: Tabletop & Team Charter

No new flags today. Two exercises, done as a team, then debriefed together.

## Exercise 1 — Board Triage 🚩

Below is a scoreboard snapshot from a hypothetical 6-hour jeopardy event using **solve-count decay** (values shown are *current*, after the listed solves) with **first-blood bonuses** and **no wrong-answer penalty**. You are **90 minutes in** on a five-person team. Nothing is solved yet.

| # | Challenge | Category | Current value | Solves | Notes |
|---|---|---|---|---|---|
| 1 | Warm Bytes | Crypto | 55 | 214 | Base64 chain |
| 2 | Query String | Web | 120 | 96 | Login form, error messages leak |
| 3 | Snapshot | Forensics | 90 | 131 | Single JPEG |
| 4 | Chatter | Network | 210 | 41 | 40 MB pcap |
| 5 | Locked Room | Forensics | 400 | 6 | **Unlocks two challenges** |
| 6 | Vaultkeeper | Reverse | 460 | 3 | x86-64 binary |
| 7 | Ghost Account | OSINT | 150 | 74 | Username pivot |
| 8 | Overflow Ave | Pwn | 480 | 1 | Remote service |
| 9 | Sidechannel | Crypto | 380 | 9 | Timing-based |
| 10 | Public Record | OSINT | 65 | 188 | Corporate filings |

**Your team:** one strong forensics/network player, one strong web player, one mid-tier crypto player, one generalist who scripts well, one newcomer.

Produce, in ten minutes:

1. **The first three challenges** each person picks up, and why.
2. **What you deliberately skip**, and why.
3. **Your re-survey checkpoint** — when do you stop and reassess?
4. **The one challenge you'd assign the newcomer**, paired with whom.
5. **Where AI helps here**, assuming it's permitted — and where you would *not* use it.

There's no single right answer. Defend your reasoning; that's the exercise.

## Exercise 2 — Team Charter

Fill in and keep. This is what you bring to your next real event.

- **Category coverage table** — every row above with a named primary and backup.
- **Functional roles** — captain, scribe, submitter, floaters.
- **Comms** — voice channel, text channel, and the norm for announcing claims and solves.
- **Shared notes** — where it lives, who owns it, the column format.
- **Timebox** — the agreed number of minutes before someone surfaces from a stuck challenge.
- **AI policy** — what your team will use, who checks the event rules, and how you log usage.
- **Break plan** — for a 6-hour event and for a 48-hour event.

---

## Instructor Rubric — Mock CTF Performance

Score each team 1–4 per dimension. This assesses *how they competed*, not how many flags they got — a team that scores well here will out-perform its raw skill level within two events.

| Dimension | 1 — Absent | 2 — Emerging | 3 — Proficient | 4 — Exemplary |
|---|---|---|---|---|
| **Board triage** | Started at the top-left and ground down | Some prioritization, no reasoning stated | Clear point-to-effort reasoning; played to strengths | Adapted to decay/unlocks; re-surveyed on schedule |
| **Category coverage** | Whole categories untouched | Coverage clustered in one or two areas | Every category attempted by a named owner | Primary *and* backup per category; no dark areas |
| **Role clarity** | No roles; everyone freelancing | Roles named but not used | Captain, scribe, submitter active throughout | Roles rotated deliberately; newcomers led with backstop |
| **Collaboration & notes** | No shared notes; duplicated work | Notes exist but go stale | Live notes; claims announced; clean handoffs | Handoffs include tried-steps and output, not just conclusions |
| **Flag discipline** | Repeated format errors; guessing | Occasional format errors | Format verified; single submitter; flags logged | Zero format errors; flags submitted immediately on discovery |
| **Time & energy** | Tunneled for hours; visible burnout | Some timeboxing, inconsistently applied | Timeboxes honored; breaks taken; last hour consolidated | Managed the clock as a team; morale actively maintained |
| **AI use** | Policy unread, or answers submitted unverified | Used ad hoc; verification inconsistent | Policy known; every AI claim checked against the artifact | Delegated mechanical work deliberately; time-boxed; usage logged |
| **Debrief quality** | No reflection | Listed what they solved | Identified where they tunneled and why | Named specific process changes for the next event |

**Debrief prompts:** Where did you tunnel? What did you skip that you shouldn't have? Which handoff worked, and which one lost time? What will you do differently in the first ten minutes next time?

---

## Team Toolkit

- **Scoreboard / platform** — most events run on **[CTFd](https://ctfd.io)** or similar; learn to read its board and submit cleanly.
- **Shared notes** — a single live doc (Google Doc, HedgeDoc, a shared Markdown file) everyone edits.
- **Communication** — a voice or text channel with a norm of announcing claims and solves.
- **A flag/challenge tracker** — even a simple table: challenge · owner · status · flag.
- **A pre-flight checklist** — VM boots, tools installed, wordlists downloaded, rules and AI policy read.

---

## Wrap-up

**Recap:**

1. Know the **format** and the **scoring model** before you touch a challenge — they define what "best move" means.
2. Individual games reward breadth; team games reward depth plus coordination. Train for the one you're entering.
3. Cover every category with a **primary and a backup**, and mix experience levels on purpose.
4. AI is a force multiplier on the mechanical and a liability on the novel — read the policy, verify everything, keep a human on the submit button.
5. Clear roles, shared notes, flag discipline, and timeboxing turn a crowd into a team.

**Where to go next:** find an event on **[CTFtime](https://ctftime.org)**, bring your team charter, and treat the first one as practice at *competing* rather than a test of what you know.

---

## Before Your Next Event

1. **Run a mini-practice** — grab a handful of challenges from [picoCTF](https://picoctf.org) and solve them as a team, with roles and a shared notes doc, on a clock.
2. **Fill in the coverage table** — and go recruit or train for whatever row is still empty.
3. **Review your weakest category** — everyone should be able to grab the easy flags in *every* area, even outside their specialty.
4. **Read one event's full rules end to end**, including the AI policy, so you know what you're looking for next time.

---

## Resources

- **[CTFtime](https://ctftime.org)** — calendar of upcoming CTFs and team rankings; find events to enter.
- **[picoCTF](https://picoctf.org)** — practice challenges across every category, any time.
- **[National Cyber League](https://nationalcyberleague.org)** — Individual and Team Games; the clearest place to feel the difference.
- **[US Cyber Games](https://www.uscybergames.com)** — the pipeline to national-team play.
- **[CTFd](https://ctfd.io)** — the platform many events run on.

---

*CTF Training Series*

