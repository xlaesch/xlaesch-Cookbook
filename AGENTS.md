# Cyber Notes — Agent Guide

This is an Obsidian vault holding personal offensive-security notes. Two content trees:

- `Labs/` — per-box walkthroughs (free-form, HTB/THM writeups).
- `network-pentesting/` — the technique reference. **Style-consistency matters here.** New/edited content below refers to this tree.

## Where things live (cookbook taxonomy)

Numbered top-level directories by kill-chain phase:

| Dir | Phase |
|---|---|
| `01-reconnaissance/` | OSINT, subdomains, crawling |
| `02-enumeration/` | Service/port/host enum (`services/`, `nmap/`, `web/`) |
| `04-exploitation/` | Service attacks, web vulns, shells, frameworks (`services/`, `web/`, `web/applications/`) |
| `05-privilege-escalation/` | Linux + Windows privesc |
| `06-post-exploitation/` | Credential access, file transfer, pivoting, C2 |
| `07-active-directory/` | AD-specific enum/abuse/trusts |
| `08-reporting/` | Reporting + notetaking |

When adding a note:
- **New technique on a known service/app** → put it in the existing phase dir (e.g. a new AD abuse → `07-active-directory/`; a new service attack → `04-exploitation/services/`).
- **Extend before you create.** If a note already covers the technology, add a section to it rather than spawning a new file. Only create a new file for a genuinely distinct technology not covered anywhere (e.g. a new service, a new AD attack family).
- **Filenames** are lowercase, hyphen-separated, technology-first: `ftp.md`, `acl-abuse.md`, `kerberoasting.md`, `command-injection.md`.

## Markdown style

Match the surrounding notes. Obsidian-flavored CommonMark.

### Structure of a note
1. One short opening paragraph (1-3 sentences) explaining **what** the thing is and **why** it matters. No heading above it.
2. `#` top-level sections per workflow step or sub-technique.
3. `##` / `###` for nested detail.
4. Terse prose: one or two sentences giving context, then the command. Do not narrate the command in prose after showing it.

### Code blocks
- Always fenced with a language tag: ` ```shell `, ` ```powershell `, ` ```python `, ` ```cmd `, ` ```text `.
- Explain each non-obvious command with an **inline `#` comment inside the block**, not in surrounding prose:
  ```shell
  # fix "Permissions 0644 for 'id_rsa' are too open"
  chmod 600 id_rsa
  ```
- Placeholders are `<angle_brackets>` for target-specific values (`<DC>`, `<user>`, `<new_password>`) or `$UPPERCASE` for shell-style variables (`$HOST`, `$SHARE`). Pick whichever matches the surrounding note; don't mix within one note.

### Tables
- Bold header cells: `| **Setting** | **Description** |`.
- Use tables for enumerated options/settings/mappings (e.g. config directives, ACE rights, injection operators), not for prose.

### Callouts
- `>` blockquotes for one-liner caveats, warnings, and clarifications. Keep them short. Example:
  > The `23` value for `setuserinfo2` corresponds to the password-replace info level.

### Obsidian specifics
- Image embeds use the wikilink embed syntax: `![[filename.png]]`.
- Do not convert these to standard markdown image syntax.

## Content conventions
- No emojis anywhere.
- No "I" / "we did" voice in the cookbook — write as reference (imperative or third person). First-person walkthroughs belong only in `Labs/`.
- Link to sibling notes by relative path when a technique depends on another (`[Kerberoasting](kerberoasting.md)`).
- Prefer native/LOTL tooling and note the tool first, then flags. Keep example IPs/hosts clearly fake or from HTB ranges (`10.129.x.x`, `10.10.14.x`).
- When documenting a failure/troubleshooting path (e.g. "No credentials are available in the security package"), lead with the **error string** verbatim so it's greppable, then the cause, then the fix.

## Verification before finishing
- After editing, re-read the changed section to confirm heading depth, code-fence language tags, and placeholder style match the rest of the file.
- Do not touch `SUMMARY.md` or `REORG_SOURCE_MAP.md` unless explicitly asked — they track structure separately.
