# CLAUDE.md — nmap builder

## Project overview
A single-file, zero-dependency web UI that helps users build nmap commands via cascading section cards. Lives on GitHub Pages. No backend, no build step, no npm — open `index.html` and it works.

## Tech stack
- Pure HTML + CSS + vanilla JS (ES6)
- Single file: `index.html` (everything inline — styles, scripts, markup)
- Google Fonts: IBM Plex Mono (loaded via CDN, graceful fallback to system monospace)
- Deployed via GitHub Pages (main branch / root)
- No frameworks, no bundlers, no package.json

## File structure
```
/
├── index.html       ← the entire app
├── README.md        ← usage + deploy instructions
└── CLAUDE.md        ← this file
```

## Design system
- **Theme**: Tokyo Night — deep navy-purple (`#1a1b2e`) background, lavender-white text, green accents, purple highlights
- **Font**: IBM Plex Mono throughout (monospace-first tool aesthetic)
- **Accent colors**: `--g0 #9ece6a` (green/nmap), `--bl #7aa2f7` (blue/flags), `--am #e0af68` (amber/target), `--pu #bb9af7` (purple/labels)
- **Motion**: CSS-only radar sweep animation in header, `max-height` card transitions, no JS animation
- No animations beyond brief copy-button confirmation flash

## Architecture

### Core data structure
```js
const SECS = [
  {
    id: 'sectionId',
    label: 'Section Label',
    groups: [
      {
        label: 'group label — choose one',
        opts: [
          { l: 'pill label', f: '-flag', d: 'man page description', xg: 'exclusiveGroup', aa: true }
        ]
      }
    ]
  }
]
```

### Option properties
- `l` — pill display label
- `f` — nmap flag string (what goes in the command)
- `d` — description shown in the desc bar on hover
- `xg` — exclusive group ID (only one option per xg can be selected at a time)
- `aa` — auto-advance to next section after selection (use for single-answer exclusive groups)
- `ni` — needs inline value input
- `it` — input type (`'number'` | `'text'`)
- `id` — input default value
- `ip` — input placeholder
- `build(v)` — custom flag formatter when the flag depends on the input value

### State object
```js
const S = {
  target: '',      // raw target string
  valid: false,    // whether target passes validation
  active: -1,      // index of currently expanded card
  reached: -1,     // furthest section index ever revealed
  sel: {},         // secId → [{fi: flatIndex, v: value}]
  nseSel: new Set(), // selected NSE scripts
  nseCat: 'all',   // active NSE category filter
  nseQ: '',        // NSE search query
  pbCat: 'all',    // playbook category filter
  pbQ: '',         // playbook search query
  tool: 'nmap',    // 'nmap' | 'rustscan'
}
```

### Key functions
- `buildCards()` — DOM construction, called once on init
- `togglePill(secId, fi)` — handles exclusive groups, auto-advance, state update
- `getFlags()` — builds flat array of flag strings from current state
- `buildCmd(tool)` — assembles complete command string for given tool
- `updateCmd()` — re-renders the command strip with syntax highlighting
- `validateTarget(s)` — returns true for valid IPv4, CIDR, IP range, or FQDN
- `renderNseList()` — re-renders NSE script pills based on cat/search state
- `renderPbList()` — re-renders playbook entries with current target filled in

## nmap section order (follows the manual)
1. Target specification (handled by the target input row, not a card)
2. Host Discovery
3. Scan Technique
4. Port Scope
5. Service / Version
6. OS Detection
7. Timing & Performance
8. Evasion / Spoof
9. Output
10. NSE Scripts

Followed by a **standalone Playbook section** (not a card — always visible at bottom of page).

## UX rules
- Cards reveal sequentially as user progresses
- Clicking any completed (done) card strip re-expands it
- No "Skip" options — `next section →` button advances without selecting
- Multi-select within sections unless flagged with `xg` (exclusive group)
- `aa: true` options auto-advance after 360ms (used for single-answer sections)
- Description bar is fixed height (32px) — content swaps, layout never shifts
- Playbook is always fully rendered, independent of the card stack
- The command strip at the top is the only place the command appears — no duplication

## What Claude should NOT do
- Do not add a backend, server, or API calls
- Do not introduce npm, webpack, vite, or any build tooling
- Do not add authentication or user accounts
- Do not use React, Vue, Svelte, or any JS framework
- Do not store any user data — this tool is fully stateless
- Do not add features not sourced from the nmap manual or the nmap NSE database
- Do not split into multiple files — everything stays in `index.html`
- Do not add a watermark/background command — the command strip at top is sufficient

## GitHub Pages deploy
- Repo settings → Pages → Source: main branch, / (root)
- URL pattern: `https://<username>.github.io/<repo-name>/`
- Update the live link in README.md after first deploy

## How to extend

### Add a new nmap section
1. Add an object to `SECS` in the correct manual order
2. Follow the `{ id, label, groups: [{ label, opts: [...] }] }` shape
3. `buildCards()` and `getFlags()` pick it up automatically
4. Add a man page URL to the `MAN` object for the section ref link

### Add a playbook command
1. Add to the `PLAYBOOK` array: `{ id, cat, title, cmd, desc }`
2. Use `<TARGET>` as the target placeholder in `cmd`
3. Choose a `cat` from `PB_CATS` or add a new category there

### Add a new NSE category
1. Add scripts to the `NSE` object: `NSE.mycat = ['script-name', ...]`
2. Add `'mycat'` to `NSE_CATS`
3. Add descriptions to `NSE_D` for each new script
