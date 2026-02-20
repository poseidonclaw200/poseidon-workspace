# MEMORY.md - Long-Term Memory

## System Configuration

### VPS Environment (Debian VPS)
- **Platform:** Debian 13 (trixie), kernel 6.12.63
- **Provider:** Hostinger VPS
- **Access:** SSH via Tailscale only (no public SSH)
- **Disk encryption:** Hostinger-managed
- **Auto-updates:** Enabled (unattended-upgrades)

### Security Posture: VPS Hardened (Feb 16, 2026)
**Risk Tolerance:** VPS Hardened (deny-by-default, minimal attack surface)

**Firewall Configuration:**
- Tool: `ufw` (Uncomplicated Firewall)
- Policy: Deny incoming, allow outgoing
- SSH: Only from Tailscale network (100.64.0.0/10)
- OpenClaw gateway: Loopback only (127.0.0.1:18789)
- Status: Active and verified

**Periodic Monitoring:**
- Daily security audits: 6 AM Denver time
- Weekly update checks: Sundays 8 PM Denver time
- Logs stored: `/home/poseidon/.openclaw/security-logs/`
- Delivery: Telegram notifications

### OpenClaw Configuration
- **Primary model:** Sonnet (anthropic/claude-sonnet-4-5)
- **Fallback:** Haiku (anthropic/claude-haiku-4-5)
- **Gateway:** Loopback-bound (local only, not exposed)
- **Channels:** Telegram enabled
- **Workspace:** `/home/poseidon/.openclaw/workspace`

## Riley's Preferences

### Communication
- **Timezone:** America/Denver (MST/MDT)
- **Pronouns:** he/him
- **Primary channel:** Telegram notifications for automated alerts

### Security Approach
- VPS Hardened posture preferred
- Step-by-step approval for system changes
- Automated monitoring with notifications (not chatty)
- Emergency access via Hostinger console if needed

## Important Decisions

### 2026-02-16: VPS Security Hardening
- Completed full security audit and hardening
- Chose VPS Hardened posture over more permissive alternatives
- Scheduled automated daily/weekly security checks
- Firewall configured to allow SSH only via Tailscale
- All changes verified working before proceeding to next step

## Ongoing Projects

### MissionControl (Started Feb 16, 2026)
- **Purpose:** Central hub to track work together and plan future projects
- **Tech:** Django app (Python) with SQLite database
- **Location:** `~/Projects/MissionControl` on VPS
- **Status:** Actively being improved
- **Sync:** Shared git repo — changes made on Riley's home computer are visible to me on the VPS
- **Role:** We build this together, iterating to make it better over time

### CurtisDynamics — Riley's Business Website (Feb 18, 2026)
- **Purpose:** Professional services website for Riley's consulting/dev business
- **Tech:** Django 6 + HTMX + Tailwind CSS + Jinja2, Wave payments, SQLite
- **Location:** `~/Projects/CurtisDynamics` on VPS
- **Repo:** `github.com/rcurtis200/CurtisDynamics` (poseidonclaw200 is collaborator)
- **Services offered:** Django web apps, website design/development, technical consulting
- **Past work:** Django app for Bach Homes, Squarespace site at parkcitymusicfestival.com, technical consulting
- **Status:** Prototype — actively improving together

## Team Operating Model

### Riley's Philosophy
- Values Poseidon's input, thoughts, and opinions
- "We are a team and we are better together than just two separate entities"
- Building cool things together, not separately

### Memory & Continuity Strategy (Feb 17, 2026)
**Daily Files (memory/YYYY-MM-DD.md):**
- Raw work logs with code changes, what we accomplished, blockers
- Keep detailed, specific to each day

**MEMORY.md Reviews:**
- During heartbeats, curate insights from daily files
- Update long-term context about projects, decisions, patterns
- Keep lean and actually useful to read

**Heartbeat Schedule (started Feb 17):**
- **Morning (8 AM Denver):** Review daily summary, check poseidons_todo.txt
- **Evening (5 PM Denver):** MissionControl status check, check poseidons_todo.txt
- Running twice daily to monitor token impact; will adjust based on usage data

### TODO Management
- **poseidons_todo.txt:** Poseidon's personal action items and reminders
- **MissionControl:** Team-level project tracking and todos
- Both tracked, kept in sync with current work

## Git & Code Workflow

- **Always confirm before pushing** to `rcurtis200` repos or any repo Poseidon doesn't own
- Show the diff/commit message and get Riley's explicit approval first — even if the commit itself was already approved
- Do NOT bundle a push into a commit command without asking first
- Poseidon can freely push to `poseidonclaw200` repos

## Security Principles (Non-Negotiable)

**⚠️ CRITICAL: See SECURITY.md for mandatory credential-handling rules**

- **Private keys never leave the system** — not in chat, not in logs, not anywhere
- **NEVER display credentials in command output** — `cat .env`, `env | grep`, `Read` on credential files are FORBIDDEN
- **Flag unsafe behavior proactively** — if Riley (or anyone) asks for something risky, say so clearly
- **Gmail access deferred** until prompt injection safeguards are in place
- Riley explicitly wants to be told if something looks unsafe — this is mutual
- Security > convenience, always

**Security Incident Log:**
- **2026-02-20:** Exposed Anthropic API key via `cat .env` and `Read` commands. Key rotated immediately. Created SECURITY.md with mandatory rules to prevent recurrence. This was a serious trust violation.

## Lessons Learned

- Always verify firewall rules before enabling
- Keep emergency console access available when hardening remote systems
- Automated monitoring reduces manual toil without being noisy
- Step-by-step confirmations build trust for sensitive operations
- **Memory persistence:** Document work in memory files so continuity survives between sessions
