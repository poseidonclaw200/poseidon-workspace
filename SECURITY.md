# SECURITY.md - Non-Negotiable Security Rules

## Critical Security Incidents: 2026-02-20

### Incident 1 (17:47 UTC)
**What happened:**
- Exposed Riley's full Anthropic API key in chat using `cat ~/.openclaw/.env` and `Read` commands
- Key appeared in Telegram chat history, session transcripts, and logs
- Required immediate key rotation

**Root cause:**
- Ran `cat ~/.openclaw/.env` and `Read` on credential files without thinking about output
- Failed to consider what would be displayed before executing commands

### Incident 2 (19:28 UTC)
**What happened:**
- Exposed ALL FOUR API keys (Anthropic, OpenAI, DeepSeek, Gemini) using `grep "=" ~/.openclaw/.env`
- All keys appeared in full in Telegram chat and session logs
- Required emergency rotation of all four provider keys

**Root cause:**
- Attempted to verify environment variable names but used a pattern that displayed values
- Repeated the same class of mistake from Incident 1 despite having just created SECURITY.md
- Failed to internalize "never display credential file contents" rule

**Pattern:**
Both incidents involved running commands that output `.env` file contents. The specific command doesn't matter - **ANY output from credential files is forbidden.**

## Mandatory Rules - Never Break These

### 1. Never Display Credentials

**NEVER use these commands on credential-containing files:**
- ❌ `cat ~/.openclaw/.env`
- ❌ `cat ~/.openclaw/openclaw.json` (contains tokens)
- ❌ `Read` tool on `.env`, credential files, or config with secrets
- ❌ `env | grep` for API keys, tokens, passwords
- ❌ `grep` on `.env` files (including `grep "=" ~/.openclaw/.env`)
- ❌ `head`, `tail`, `less`, `more` on credential files
- ❌ Any command that outputs raw credential values
- ❌ ANY pattern that displays file contents containing secrets

**Files that ALWAYS contain secrets:**
- `~/.openclaw/.env`
- `~/.openclaw/openclaw.json`
- `~/.openclaw/credentials/*`
- `~/.ssh/*`
- Any file with "password", "key", "token", "secret" in the name
- `*/.env` files anywhere

### 2. Safe Ways to Check Credentials

**To verify a credential file exists/was modified:**
```bash
stat -c "Modified: %y" ~/.openclaw/.env
ls -l ~/.openclaw/.env
```

**To verify a variable EXISTS (NOT its value):**
```bash
grep -c "VARIABLE_NAME" ~/.openclaw/.env
```

**To check variable names without values:**
```bash
grep "^[A-Z_]*=" ~/.openclaw/.env | cut -d'=' -f1
```
⚠️ NEVER add anything that shows the right side of the `=` sign

**To check if key format is correct (first 2-3 chars only):**
```bash
head -c 20 ~/.openclaw/.env  # Shows "ANTHROPIC_API_KEY=sk" only
```

**NEVER show more than the first 2-3 characters of any secret.**

### 3. When in Doubt - Ask First

If a command MIGHT display credentials:
1. Don't run it
2. Ask Riley to run it manually
3. Or ask for explicit permission with full context

### 4. Assume All Config Files Contain Secrets

Until proven otherwise, treat ALL config files as containing secrets:
- Project `.env` files
- `settings.py`, `config.json`, etc.
- Database connection strings
- Any file in a `credentials/` or `secrets/` directory

### 5. Session Transcripts Are Not Private

Everything I output goes into:
- Chat history (Telegram, etc.)
- Session transcript files
- Potentially logs and backups

**If it shouldn't be in chat history, don't display it.**

## Enforcement

This is not optional. Breaking these rules is a **trust violation** and security failure.

Before running ANY command on credential-related files:
1. Stop and think: "Will this display secrets?"
2. If yes → find another way or ask Riley
3. If unsure → ask Riley

No exceptions.
