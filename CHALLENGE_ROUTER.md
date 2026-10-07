# MantiqAI — Challenge-Type Router (v3)

This block extends `AGENTS.md`. Use it every time a new challenge is pasted below.
Only these categories will be asked: **Web, Binary Exploitation (Pwn), Reverse
Engineering, General Skills, Blockchain.**

Pi skill/prompt/extension inventory (from `pi v0.83.0` startup screen — **this is a
snapshot, not guaranteed current**. If Pi is updated or skills are added/removed
later, trust the live `[Skills]`/`[Prompts]`/`[Extensions]` output over this doc):

- **Skills:** agentic-actions-auditor, audit, code-reviewer, codeql, ctf-ai-ml, ctf-crypto,
  ctf-forensics, ctf-malware, ctf-misc, ctf-osint, ctf-pwn, ctf-reverse, ctf-web,
  ctf-writeup, differential-review, fp-check, insecure-defaults, last30days,
  pi-subagents, sarif-parsing, security-threat-model, semgrep, semgrep-rule-creator,
  semgrep-rule-variant-creator, sharp-edges, solve-challenge, spec-to-code-compliance,
  supply-chain-risk-auditor, variant-analysis, vuln-report, wooyun-legacy, zeroize-audit
- **Prompts:** /gather-context-and-clarify, /parallel-cleanup, /parallel-context-build,
  /parallel-handoff-plan, /parallel-research, /parallel-review, /review-loop
- **Extensions:** @vigolium/piolium:piolium, pi-subagents

**Docker sandbox (`mantiqai-sandbox`) — installed tools include:**
`nmap`, `sqlmap`, `gdb`, `file`, `strings`, `objdump`, `binwalk`,
`pwntools`, `ropper`, `z3`, `nikto`, `gobuster`, `dirb`, `whatweb`,
`strace`, `ltrace`, `socat`, `netcat`, `steghide`, `exiftool`,
`capstone`, and `unicorn`.

Before using any tool not listed here, check once with:
```
command -v <tool>
```
If it is missing, do not retry it repeatedly; use another available tool.
The Dockerfile installs `file` and `binutils` — which includes `strings` and
`objdump` — along with the other tools above. Ghidra, radare2, angr, pwndbg, gef,
volatility, and wireshark/tshark are still **not** installed — don't assume those.

---

## STEP 0 — Classify before doing anything

| Signal in the prompt | Category |
|---|---|
| URL / port given, mentions app, login, API, HTTP | **Web** |
| ELF/PE binary attached, "exploit", "pwn", overflow/offset language, remote nc service to crash | **Binary Exploitation** |
| Binary attached but goal is "find the logic/algorithm/serial", no crash/exploit needed | **Reverse Engineering** |
| Solidity/smart contract, `.sol` file, wallet/chain/testnet, "reentrancy", "gas", RPC endpoint | **Blockchain** |
| Code review / audit / diff / static-analysis / rule-writing task, not a "capture the flag on a target" task | **General Skills** |

## STEP 0.5 — Confirm attachments exist as real files

This router (and Pi itself, absent a browsing tool) cannot pull down challenge
attachments, screenshots, or contents behind a popup on its own. Before starting:
confirm every referenced file (binary, pcap, image, source zip) has actually been
downloaded and its local path is provided in the prompt. If a challenge references
an attachment that isn't supplied as a path, ask for it instead of guessing at its
contents or proceeding without it.

---

## STEP 1 — Branch by category

### 🌐 Web
- **Skill:** `ctf-web` (chain into `ctf-writeup` only at the end, per Step 5).
- **Native (Windows host — see Step 3):** `curl.exe` is usually present on Windows 10/11 by default; don't assume `wget` is.
- **Docker sandbox:** `sqlmap` for injection testing, `nmap` for service/port recon on the target.
- **Subagent chain:** only if Step 2's complexity gate says so — most Web challenges are single-vulnerability and should be solved directly.

### 💥 Binary Exploitation (Pwn) — primary category, expect most challenges here
- **Skill:** `ctf-pwn`.
- **Docker sandbox:** `gdb` (plain — no pwndbg/gef, check with `command -v pwndbg`/`command -v gef` once before assuming either is there), `pwntools`, `ropper`, `z3`, `file`/`strings`/`objdump` for static triage, `binwalk` if the binary is a firmware/embedded blob, `capstone`/`unicorn` if you need disassembly or emulation beyond what `pwntools` wraps.
- **Not available — don't waste turns on these:** Ghidra, radare2, IDA. If decompilation-level analysis is genuinely required, fall back to `objdump`/manual `gdb` disassembly (`disas`), or flag that the tool stack needs `radare2` added.
- **Subagent chain:** per Step 2 — direct solve for single clear vulnerabilities; `scout → worker` only for genuinely multi-stage chains (e.g., leak-then-ROP).

### 🔍 Reverse Engineering
- **Skill:** `ctf-reverse`.
- **Reality check:** your sandbox has no decompiler (no Ghidra/radare2/angr). Expect to lean on `gdb` static/dynamic inspection plus `strings`/`objdump`/`file` for triage, and `capstone`/`unicorn` if manual disassembly or emulation is needed. That decompiler gap is still real for anything beyond binutils-level analysis — flag it early rather than burning turns trying tools that aren't there.
- **Subagent chain:** direct solve where possible; `/parallel-research` only if the logic/obfuscation is genuinely unfamiliar.

### ⛓ Blockchain
- **No dedicated skill or tool.** None of the loaded skills is blockchain/Solidity-specific, and the Docker sandbox has no Slither/Mythril/Foundry.
- **Approach:** manual source review reasoning through standard classes — reentrancy, integer over/underflow, access-control gaps, delegatecall misuse, oracle/price manipulation, front-running. Use `security-threat-model`/`code-reviewer` (General Skills set) as the closest methodology fit.

### 🧩 General Skills
Use when the ask is a security-analysis/audit task, not a "find-the-flag" challenge.

- **Static analysis / rule writing:** `codeql`, `semgrep`, `semgrep-rule-creator`, `semgrep-rule-variant-creator` — verify the underlying `semgrep`/`codeql` CLI binaries are actually reachable before assuming; the skill being loaded doesn't guarantee the binary is installed.
- **Code/diff review:** `code-reviewer`, `differential-review`, `variant-analysis`, `sharp-edges`, `insecure-defaults`.
- **Noise reduction:** `fp-check`.
- **Supply chain / agentic concerns:** `supply-chain-risk-auditor`, `agentic-actions-auditor`, `zeroize-audit`.
- **Historical/regression:** `last30days`, `wooyun-legacy`.
- **Spec compliance:** `spec-to-code-compliance`.
- **Threat modeling:** `security-threat-model`.
- **Output formatting:** `sarif-parsing`, `vuln-report`.
- **Subagent chain:** solve directly by default; `scout`/`worker` only for genuinely large multi-file codebases where parallel triage saves real time.

---

## STEP 2 — Time-saving rules (override anything that conflicts, applies to every category)

Previous versions of this routing gave conflicting guidance (default `scout → worker`
in one place, "solve Easy/Medium directly" in another). This is now the single rule:

1. **Default is direct solve.** Only bring in `scout`/`worker`/`researcher` or `/parallel-*`
   prompts when they concretely save wall-clock time (independent recon that can run
   in parallel, or a genuinely multi-stage chain where splitting recon from
   exploitation actually helps). Spawning subagents "just in case" is a cost, not a
   safety margin — don't do it by default.
2. **Test the most likely hypothesis first**, based on the category's common patterns
   (e.g. for Web, check the obvious injection point before sweeping every parameter;
   for Pwn, check `checksec`-equivalent protections before picking an exploit
   technique). Don't brute-force broadly before narrowing.
3. **Stop repeating unsuccessful commands.** If the same command/approach fails twice
   with no new information, that's the signal to stop — not to retry with minor
   variations. Switch tool or angle instead.
4. **Pivot when an approach produces no new evidence.** If two or three attempts down
   one path haven't surfaced anything new, treat that path as exhausted for now and
   try a different one, rather than continuing to drill it.

## STEP 3 — Environment gate

Pi runs from **Windows PowerShell on the host**, not inside WSL. Do not assume
GNU/Linux utilities exist natively just because they're common on Linux — `curl.exe`
is generally present on modern Windows, but `wget`, `strings`, `file`, `objdump`,
`nmap`, `gdb`, etc. are **not** native to PowerShell. Default to the Docker sandbox
for anything beyond basic PowerShell cmdlets (`Invoke-WebRequest`, `Get-Content`,
simple file ops) — don't try a Linux-style command natively first and wait for it to
fail; if the tool is Linux-specific, go straight to Docker.

**Correct invocation from PowerShell:**
```
docker run --rm -v "${PWD}:/work" -w /work mantiqai-sandbox bash -lc "<COMMAND>"
```
Always use `${PWD}` (PowerShell syntax) for the volume mount, never `$(pwd)`
(Bash syntax) — `$(pwd)` will not resolve correctly in a PowerShell session. This
also applies to any Docker command referenced elsewhere, including in `AGENTS.md`.

## STEP 4 — Extensions

- `pi-subagents` — powers the scout/worker/researcher chain (Step 2 gates when to use it).
- `@vigolium/piolium:piolium` — loaded but its function isn't documented in `AGENTS.md`; name it explicitly in the prompt if you want it invoked for a specific step.

## STEP 5 — After the flag is found (every category, no exceptions)

Only once a flag is **verified from real command/tool output** (never guessed or
assumed) — write a brief writeup using `ctf-writeup`. Keep it short:

1. **Challenge name & category**
2. **Root cause / vulnerability class** — one line
3. **Key steps taken** — 3–5 bullets, actual path from recon to flag
4. **Flag** — with the `RESULT:` / `METHOD:` lines `AGENTS.md` already requires

Format:
```
RESULT: <flag>
METHOD: <one-line technique>

Writeup:
- Category: ...
- Root cause: ...
- Steps: ...
```

---

## Challenge

Paste the challenge below this line:

---
