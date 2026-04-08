# **[nmapguide](https://homestarfuzzer.github.io/nmapguide/)**

An interactive, browser-based nmap command builder and reference tool for pentesters, red teamers, and students learning network scanning.

---

## What it is

Build valid nmap commands step by step through a cascading card interface — no manual flag memorization required. Each section follows the nmap man page order exactly. Hover any option for its man page description. Copy your command at any point.

Includes a **Playbook** at the bottom: 34+ real-world commands organized by use case (CTF, recon, stealth, web, SMB, services, firewall evasion, vuln scanning) that auto-fill your target.

---

## Features

- **9 sections** in man page order: Host Discovery → Scan Technique → Port Scope → Service/Version → OS Detection → Timing → Evasion/Spoof → Output → NSE Scripts
- **Multi-select** where nmap allows it (ping methods, output flags, evasion), **exclusive groups** where it doesn't (timing templates, TCP techniques)
- **NSE script browser** — 100+ scripts across 13 categories with full descriptions, global search
- **rustscan tab** — converts your nmap flags to rustscan syntax (`rustscan -a <target> -r <ports> -- [flags]`)
- **Live command display** — syntax-highlighted command updates on every selection
- **Playbook** — 34 plug-and-play commands for real engagements. Enter your target once and every playbook command auto-fills
- **Man page links** — every section has a direct `man ↗` link to the relevant nmap reference page
- **Target validation** — red while incomplete/invalid, amber when valid IP/CIDR/hostname
- Zero dependencies · Zero build step · Single HTML file

---

## Options covered

| Section | Coverage |
|---|---|
| Host Discovery | -Pn, -sn, -PS, -PA, -PU, -PE, -PP, -PM, -PR, -n, -R |
| Scan Technique | -sS, -sT, -sU, -sA, -sW, -sM, -sN, -sF, -sX |
| Port Scope | -p-, -F, -p 1-1024, custom, --top-ports, --open, --exclude-ports |
| Service/Version | -sV, -A, --version-intensity 0/5/9, --version-light, --version-all |
| OS Detection | -O, --osscan-guess, --osscan-limit |
| Timing | -T0–T5, --min-rate, --max-rate, --min-parallelism, --max-retries, --host-timeout |
| Evasion | -f, -f -f, -D RND:N, --source-port, --data-length, --randomize-hosts, --badsum, --ttl, --spoof-mac |
| Output | -v, -vv, -d, --reason, --packet-trace, --open, -oN, -oX, -oG, -oA |
| NSE Scripts | 100+ scripts across: auth, brute, default, discovery, exploit, vuln, safe, http, smb, ssh, ssl, dns |

---
## Extending

To add a new nmap section:

1. Add a section object to the `SECS` array in `index.html`, following the existing pattern
2. Add a `groups` array with labelled option groups
3. Each option: `{ l: 'label', f: 'flag', d: 'description', xg: 'group', aa: true }`
4. `buildCommand()` picks it up automatically

To add a playbook command:

1. Add an entry to the `PLAYBOOK` array
2. Format: `{ id, cat, title, cmd: '...nmap flags... <TARGET>', desc }`
3. Use `<TARGET>` as the placeholder — it auto-fills from the target input

---

## Reference

- [nmap Reference Guide](https://nmap.org/book/man.html)
- [NSE Script Database](https://nmap.org/nsedoc/)
- [rustscan](https://github.com/RustScan/RustScan)
