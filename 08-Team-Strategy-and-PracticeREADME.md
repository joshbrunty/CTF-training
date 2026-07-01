# Guided Practice & Team Strategy — Competing as a Team

**CTF Training Series**
**Level:** All levels — this session is about *how you compete*, not a new category. Everything you've built so far comes together here.

> Your reference for this session. There's no new tool to learn today — the skill is turning six categories' worth of ability into points on a board, as a team, under a clock.

---

## Learning Objectives

By the end of this session, you will be able to:

1. Explain how jeopardy-style CTFs are scored and how that should shape your choices.
2. Read a scoreboard and **triage** challenges by value, effort, and your team's strengths.
3. Divide labor across a team — by **category** and by **role**.
4. Collaborate cleanly: shared notes, flag discipline, handoffs, no duplicated effort.
5. Manage time and energy across a long event.
6. Compete in a capstone **mock CTF** that reuses every category you've trained.

---

## From Learning to Competing

You can already solve crypto, web, forensics, and reverse-engineering challenges. Competing well is a *different* skill set layered on top:

- **Prioritization** — which challenge, in which order, with whom.
- **Coordination** — a team that doesn't step on itself beats a team of stronger soloists.
- **Stamina** — a CTF is a marathon; the team that's organized in hour six wins.

The good news: these meta-skills are learnable and they compound. Today is practice at *competing*, not just solving.

---

## How CTFs Are Scored

Most CTFs you'll enter are **jeopardy-style**: a board of challenges grouped by category (Crypto, Web, Forensics, Reverse, Pwn, OSINT…), each worth points. You submit a flag, you get the points.

Two scoring details that change your strategy:

- **Dynamic (decay) scoring** — a challenge starts at a high value that *drops* as more teams solve it. Translation: an "easy" challenge everyone solves is worth little by the end; a hard one few crack stays valuable. Early solves on soon-to-decay challenges matter.
- **Tie-breaks by time** — when two teams have equal points, the one that reached that score **first** usually wins. Submitting steadily early is worth more than a late flurry.

Some events add **first-blood** bonuses (extra credit for the first team to solve a challenge) — a reason to have your specialists hit their category fast.

---

## Read the Board, Then Triage

Don't start at the top-left and grind down. **Survey first, then choose.**

- **Point-to-effort ratio.** Grab the cheap, fast flags early to get on the board and build momentum — but don't *only* eat easy points if higher-value challenges match your strengths.
- **Play to strengths first.** Send each person at their best category before anyone touches something they'll struggle with.
- **Don't tunnel.** If a challenge has eaten 45 minutes with no progress, mark it, write down where you're stuck, and move on. Come back with fresh eyes.
- **Re-survey periodically.** Decay scoring and new solves change what's worth doing. Glance at the board every so often.

---

## Team Roles

A team works best when everyone knows their lane. Two ways to divide up:

**By category** — assign a lead per area based on who's strongest:
- Crypto lead · Web lead · Forensics lead · Reverse-engineering lead · (Pwn / OSINT as your team grows)

**By function** — lightweight roles that keep the team coherent:
- **Captain / coordinator** — watches the board, assigns and re-assigns, tracks time. Doesn't have to be the strongest solver — has to be organized.
- **Scribe** — keeps the shared notes: what's solved, who's on what, where people are stuck.
- **Floaters** — strong generalists who jump to whoever needs a second pair of eyes.

In a mixed-skill team, the categories are owned by your specialists; newer members pair with them and float.

---

## Pair Strong with New

A mixed-experience team is a feature, not a problem — if you use it deliberately.

- **Pair, don't isolate.** Put a newer member *with* a specialist on a challenge. They learn the workflow live; the specialist gets a second set of eyes and someone to explain to (which sharpens thinking).
- **Hand newer members the on-ramp challenges.** The cheap recon/metadata/encoding flags are real points *and* confidence-builders.
- **Rotate.** Over a long event, let newer members try leading a challenge with a specialist backstopping. That's how this year's beginners become next year's leads.

---

## Collaboration Mechanics

The difference between a team and a crowd is a few simple habits:

- **One shared notes doc.** Every challenge gets a line: name, category, who's on it, status (untouched / in-progress / stuck / solved), and the flag once found.
- **Claim before you start.** "I've got Crypto 3" prevents two people quietly solving the same thing.
- **Write down where you're stuck** before you walk away — so the next person doesn't restart from zero.
- **Flag discipline.** Confirm the exact format before submitting; submit the moment you have it; paste the flag into the notes so the team sees the win and nobody re-solves it.
- **Hand off cleanly.** "Here's what I tried, here's the output, here's my hunch" turns a stuck challenge into a fresh start for someone else.

---

## Time & Energy Management

A CTF is usually hours long — sometimes days. Endurance is strategy:

- **Timebox.** Cap how long anyone sinks into one challenge before they surface and re-assess.
- **Take real breaks.** Eat, walk, sleep on multi-day events. Tired solvers miss obvious things.
- **Watch the clock as a team.** Know how much time is left and what's realistically closable.
- **The last hour.** Stop starting *new* hard challenges; consolidate near-finished ones, double-check submitted flags, and grab any remaining cheap points.
- **Protect morale.** Celebrate every flag out loud. A team that feels momentum keeps finding flags.

---

## Working a Challenge as a Team

When several people attack one challenge, structure beats chaos:

1. **Recon together, split work apart.** Agree on what the challenge *is*, then divide the angles.
2. **Rubber-duck.** Explaining where you're stuck to a teammate solves it surprisingly often.
3. **Share output, not just conclusions.** Paste the actual `objdump`/`exiftool`/stream output — a second person spots what the first glossed over.
4. **Know when to call it.** Two people stuck for an hour is a signal to bring in a floater or shelve it.

---

## Common Team Failure Modes

| Anti-pattern | The fix |
|---|---|
| Everyone piles onto one shiny challenge | Spread across categories; one or two per challenge |
| Lone-wolfing — no one knows who's on what | Claim challenges in the shared notes |
| No notes, so handoffs restart from zero | Scribe + "where I'm stuck" lines |
| Wrong flag format / trailing newline | Confirm format; copy-paste exactly |
| Tunneling for hours on one problem | Timebox and re-survey the board |
| Burnout by hour six | Breaks, rotation, celebrate wins |

---

## Capstone: The Mock CTF 🚩

Today's hands-on is a **timed, scored mock CTF** that pulls one or more challenges from every category you've trained — crypto, web, reverse engineering, and forensics. Your instructor runs it as a real event:

- **Form teams** with a deliberate mix of skill levels.
- **Assign roles** — captain, scribe, category leads, floaters.
- **Survey, triage, and divide** the board before anyone dives in.
- **Keep the shared notes** and submit flags as `CTF{...}` the moment you have them.
- **Debrief afterward** — what worked, where you tunneled, what you'd do differently.

This is the dress rehearsal. Treat it like the real thing.

---

## Team Toolkit

- **Scoreboard / platform** — most events run on **CTFd** or similar; learn to read its board and submit cleanly.
- **Shared notes** — a single live doc (Google Doc, HedgeDoc, a shared Markdown file) everyone edits.
- **Communication** — a voice or text channel (Discord, in-room) with a norm of announcing claims and solves.
- **A flag/challenge tracker** — even a simple table: challenge · owner · status · flag.

---

## Wrap-up & What's Next

**Recap:**
1. Competing is prioritization + coordination + stamina, layered on solving.
2. Triage the board; play to strengths; don't tunnel.
3. Clear roles, shared notes, and flag discipline turn a crowd into a team.

**Next session (The Final CTF):** the real thing — a full-length competition putting every category and every team habit to work.

---

## Before the Final CTF

1. **Run your own mini-practice** — grab a few challenges from [picoCTF](https://picoctf.org) and solve them as a team with roles and a shared notes doc.
2. **Agree on your roles and tooling** now — captain, scribe, leads, and where the shared notes live — so it's automatic on game day.
3. **Review your weakest category** — everyone should be able to grab the easy flags in *every* area, even outside their specialty.

---

## Resources

- **[CTFtime](https://ctftime.org)** — the calendar of upcoming CTFs and team rankings; find events to enter.
- **[picoCTF](https://picoctf.org)** — practice challenges across every category, any time.
- **[CTFd](https://ctfd.io)** — the platform many events (and your mock CTF) run on.

---

*CTF Training Series*
