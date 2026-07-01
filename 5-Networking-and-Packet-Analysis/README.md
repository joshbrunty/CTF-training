# Networking & Packet Analysis — Reading the Wire

**CTF Training Series** · **Flag format:** `CTF{...}`
**Level:** Intermediate-friendly — builds on the Linux command line, and feeds straight into Forensics.

> This single README is both the **lesson** and the **lab**. Read the concepts in Part 1, then work the three captures in Part 2 — their flags aren't printed here on purpose. Work in your Kali VM with **Wireshark** and **tcpdump**.

---

## Learning Objectives

By the end of this session, you will be able to:

1. Describe the TCP/IP model, common protocols, and the ports they use.
2. Capture and read traffic with **tcpdump**, including BPF capture filters.
3. Navigate **Wireshark**: display filters, following streams, exporting objects, and the Statistics views.
4. Extract **credentials and files** from captured traffic.
5. Recognize **indicators of compromise** — cleartext secrets, scans, and covert channels.
6. Apply a repeatable packet-analysis workflow.

---

# Part 1 — The Lesson

## What Is Packet Analysis?

Every action on a network is carried by **packets**. A capture (a `.pcap`/`.pcapng` file) is a recording of those packets — and if the traffic wasn't encrypted, everything inside is readable: web requests, logins, file transfers, DNS lookups, commands.

In a CTF, you're handed a capture and a flag is hidden in the traffic. The skill is knowing **which protocol carries what**, **how to filter down to the interesting packets**, and **how to reassemble a conversation** back into something you can read.

---

## Networking Refresher

Traffic is layered. You mostly care about three layers:

| Layer | Carries | You'll look at |
|---|---|---|
| **IP** | Source/destination **addresses** | Who's talking to whom |
| **TCP / UDP** | **Ports** and reliability | Which service (port) is in use |
| **Application** | The actual data | HTTP, DNS, FTP, etc. |

- **TCP** is connection-oriented — it starts with a three-way handshake (**SYN → SYN-ACK → ACK**) and reassembles into ordered streams. **UDP** is fire-and-forget (DNS, some tunneling).
- **Ports** identify the service. Know the common ones:

| Port | Service | Encrypted? |
|---|---|---|
| 80 | HTTP | ❌ cleartext |
| 443 | HTTPS / TLS | ✅ |
| 53 | DNS | ❌ |
| 21 / 20 | FTP | ❌ cleartext |
| 22 | SSH | ✅ |
| 23 | Telnet | ❌ cleartext |
| 25 / 110 / 143 | SMTP / POP3 / IMAP | often ❌ |

> The cleartext protocols are where CTF (and real-world) secrets leak. If you see port 80, 21, or 23, someone's data is probably readable.

---

## Capturing with tcpdump

`tcpdump` is the command-line capture tool — fast, scriptable, on every box.

```bash
sudo tcpdump -i eth0                    # live capture on an interface
sudo tcpdump -i eth0 -w capture.pcap    # save to a file
tcpdump -r capture.pcap                 # read a saved file
tcpdump -r capture.pcap -A              # show packet payloads as ASCII
tcpdump -r capture.pcap -X              # hex + ASCII
```

**BPF capture filters** narrow what you capture or read:

```bash
tcpdump -r capture.pcap 'tcp port 80'           # only HTTP
tcpdump -r capture.pcap 'host 10.10.10.5'       # only traffic to/from a host
tcpdump -r capture.pcap 'icmp'                   # only ping/ICMP
tcpdump -r capture.pcap 'tcp port 21 or port 23' # cleartext logins
```

---

## Wireshark

Wireshark is the GUI powerhouse. The moves you'll use constantly:

- **Display filters** (top bar) — `http`, `dns`, `icmp`, `ip.addr == 10.10.10.5`, `tcp.port == 21`, `http.request.method == "POST"`. (Note: *display* filters use `==`; *capture*/BPF filters use the tcpdump syntax above.)
- **Follow Stream** — right-click a packet → *Follow → TCP Stream* (or HTTP Stream) to reassemble a whole conversation into readable text. This is how you read a login or a command session.
- **Export Objects** — *File → Export Objects → HTTP* lists every file transferred over HTTP; save one out to disk.
- **Statistics** — *Protocol Hierarchy* (what's in the capture), *Conversations* / *Endpoints* (who talked to whom, how much). Great for spotting the odd host.

---

## Reading a Conversation

Most flags come from **reassembling** traffic, not staring at single packets:

- **Credentials** — an HTTP `POST` login or an FTP `USER`/`PASS` sits in cleartext inside the stream. Follow it and read the fields.
- **Files** — a download over HTTP can be pulled back out whole with *Export Objects*; a file sent over another protocol can be carved from the stream bytes.
- **Commands** — a Telnet or unencrypted shell session replays keystroke-by-keystroke in *Follow Stream*.

---

## Indicators of Compromise

Analysts read captures to answer "what happened?" The tells:

- **Cleartext secrets** — passwords/tokens on port 80/21/23.
- **Port scans** — one host hitting many ports (lots of `SYN`s, few completed handshakes).
- **Data exfiltration** — data leaving over channels meant for something else: long high-entropy **DNS** subdomains, or payloads stuffed into **ICMP** (ping) packets.
- **Beaconing** — a host calling out to the same destination on a regular interval (malware check-ins).
- **Plaintext where there shouldn't be** — protocols or ports that don't match their expected traffic.

---

## The Packet-Analysis Workflow

1. **Open** the capture; skim **Statistics → Protocol Hierarchy** to see what's there.
2. **Filter** to the interesting protocol or host — cut the noise.
3. **Follow** the stream to read the conversation.
4. **Extract** — export a file, copy out credentials, or reassemble a payload.
5. **Decode** if needed (Base64/hex — your Crypto skills), then **recover** the `CTF{...}` flag.

---

## Your Toolkit

| Tool | Purpose |
|---|---|
| **tcpdump** | CLI capture and quick payload reads (`-A`, `-X`, BPF filters) |
| **Wireshark** | GUI analysis — filters, follow stream, export objects, statistics |
| **tshark** | Wireshark's CLI — scriptable field extraction |
| **NetworkMiner** | Automatic artifact/file/credential carving from a pcap |
| `base64`, CyberChef | Decode recovered data |

> On Kali these ship by default; if not: `sudo apt install wireshark tcpdump tshark`.

---

# Part 2 — The Lab: Three Captures

## Scenario

Incident responders pulled three packet captures off a network after a suspected intrusion. Each capture hides a flag. Triage them one at a time — survey, filter, follow, extract.

## Files Provided

| File | Challenge | Difficulty |
|---|---|---|
| [`creds.pcap`](./creds.pcap) | 1 — Cleartext Credentials | Easy–Intermediate |
| [`download.pcap`](./download.pcap) | 2 — File Recovery | Intermediate |
| [`icmp.pcap`](./icmp.pcap) | 3 — Covert Channel | Intermediate–Hard |

---

### Challenge 1 — Cleartext Credentials (`creds.pcap`)

Someone logged into an internal web app over plain **HTTP** — and HTTP hides nothing.

- Open the capture and look at the **HTTP** traffic. What kind of request carries a login?
- **Follow the stream** and read the request body. The password is the flag.

> Filter to get started: `http.request.method == "POST"`

### Challenge 2 — File Recovery (`download.pcap`)

A file was downloaded over HTTP. Pull it back out of the traffic and read it.

- Find the HTTP request — what file was fetched?
- Use Wireshark's **File → Export Objects → HTTP** to save the transferred file, then open it. (Or follow the HTTP stream and read the response body.)

### Challenge 3 — Covert Channel (`icmp.pcap`)

Most of this capture is ordinary **ICMP** (ping) traffic — but someone is using ping to smuggle data out. Normal pings carry filler; these don't.

- Filter to `icmp` and look at the **payloads** of the echo requests. Some carry filler; some carry something readable.
- The smuggled data is **chunked across several packets**, in order. Collect the readable payloads and put them back together.

> A ping's data field isn't supposed to be a secret. Read it.

---

## Submission

Three separate flags, one per capture:

| Challenge | Flag |
|---|---|
| 1 — Credentials | `CTF{________________}` |
| 2 — File Recovery | `CTF{________________}` |
| 3 — Covert Channel | `CTF{________________}` |

---

## Hints (Use Only If Stuck!)

<details>
<summary>Challenge 1 — nudge</summary>

Filter `http.request.method == "POST"`, right-click the POST → *Follow → HTTP Stream*, and read the `username=…&password=…` body. From the terminal: `tcpdump -r creds.pcap -A | grep -i password`.

</details>

<details>
<summary>Challenge 2 — nudge</summary>

The request is `GET /flag.txt`. *File → Export Objects → HTTP* → select `flag.txt` → *Save*, then open it. Or `tcpdump -r download.pcap -A` and read the response body after the headers.

</details>

<details>
<summary>Challenge 3 — nudge</summary>

Filter `icmp`. The exfil packets are echo requests (type 8) whose data field holds printable text instead of the usual `abcdef…` filler; they share an unusual ICMP identifier and are numbered by sequence. Read their payloads in order and concatenate. From the terminal:
```bash
tshark -r icmp.pcap -Y 'icmp.type==8' -T fields -e data.text
```

</details>

---

## Common Pitfalls

- **Confusing display and capture filters.** Wireshark's bar uses `http`, `ip.addr==…`; tcpdump/BPF uses `tcp port 80`, `host …`. Different syntax.
- **Staring at single packets.** Reassemble — *Follow Stream* and *Export Objects* do the heavy lifting.
- **Ignoring the boring protocols.** ICMP and DNS look like noise, which is exactly why data hides in them.
- **Forgetting to decode.** Extracted data is often Base64 or hex — finish with your Crypto toolkit.
- **Assuming everything's encrypted.** Port 80/21/23 traffic is wide open; check it first.

---

## Key Concepts Practiced

- **TCP/IP, ports, and protocols** — knowing which carries what
- **Capture filtering** — tcpdump/BPF vs. Wireshark display filters
- **Stream reassembly** — following a conversation back into readable text
- **Object extraction** — pulling transferred files out of a capture
- **Indicators of compromise** — cleartext creds and covert channels (ICMP)

---

## Wrap-up & What's Next

**Recap:**
1. Captures are readable evidence — if it isn't encrypted, it's in the clear.
2. Filter to the signal, then follow the stream or export the object.
3. Boring protocols (ICMP, DNS) are favorite hiding spots — check them.

**Next session (Digital Forensics):** recovering hidden and deleted data from files, disks, and metadata — the same investigative mindset, applied to storage instead of the wire.

**Before then:**
1. Practice on [Wireshark's sample captures](https://wiki.wireshark.org/SampleCaptures) and the [malware-traffic-analysis.net](https://www.malware-traffic-analysis.net) exercises.
2. Capture your own traffic: `sudo tcpdump -i any -w mine.pcap`, browse a plain-HTTP site, then open it in Wireshark.
3. Learn five display filters cold: `http`, `dns`, `icmp`, `ip.addr==`, `tcp.port==`.

---

## Resources

- **[Wireshark](https://www.wireshark.org)** — the analyzer, plus [sample captures](https://wiki.wireshark.org/SampleCaptures).
- **[tcpdump.org](https://www.tcpdump.org)** — the CLI capture tool and its manual.
- **[malware-traffic-analysis.net](https://www.malware-traffic-analysis.net)** — real-world packet puzzles.
- **[CyberChef](https://gchq.github.io/CyberChef/)** — decode extracted data fast.

---

*Prepared by Coach Josh Brunty*
*Contact: [josh.brunty@marshall.edu](mailto:josh.brunty@marshall.edu) | [coachbrunty@uscybergames.org](mailto:coachbrunty@uscybergames.org)*
*CTF Training Series*
