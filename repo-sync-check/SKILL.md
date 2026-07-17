---
name: repo-sync-check
description: Weekly repo hygiene routine — checks every project under ~/documents/claudec/* for git sync status, scans for secrets committed to history, and files/dedupes Linear issues for anything that needs attention. Use when the user asks to check repo sync status, run repo hygiene, or invokes this as a scheduled routine.
---

# Repo Sync Check

Runs in Claude Code with local filesystem + git access. Scope: `~/documents/claudec/*` — one subfolder is one project.

Resolved open question: "based on claude sessions" means a **scope filter** — only check folders with recent file activity (last N days, default 14) — not a repo-creation trigger. This keeps the routine bounded and cheap to re-run. Reassess this if the user later asks Claude to propose new repos from session content.

Output destination: a Linear comment thread. Post/update a summary each run so there's a persistent record beyond the chat.

## Step 1 — Discover projects

For each subfolder under `~/documents/claudec/`:
- Does it have a `.git` folder?
- Has it had file activity in the last N days (default 14)? Skip folders with no recent activity — that's the scope filter.

## Step 2 — Sync status

Check all six states explicitly, not a single synced/not-synced flag:

| State | Meaning | How to check |
|---|---|---|
| Untracked | No `.git` folder at all | dir has no `.git` |
| No remote | Git repo exists, no GitHub remote configured | `git remote -v` empty |
| Uncommitted changes | Remote exists, working tree is dirty | `git status --porcelain` non-empty |
| Unpushed commits | Committed locally, not pushed | `git log @{u}..HEAD` non-empty |
| Diverged | Local and remote have both moved | `git rev-list --left-right --count @{u}...HEAD` shows both sides > 0 |
| Conflicted | Merge conflict markers present | `git status` shows unmerged paths, or grep for `<<<<<<<` |

## Step 3 — Sensitive file handling (two layers)

**A. Exclude from any commit/push:** anything gitignored, `.env*`, common credential/key file patterns.

**B. Scan history for secrets already committed** — a file being gitignored today doesn't mean it wasn't committed earlier. Grep `git log -p` for API-key-shaped strings, or run `gitleaks` if available. **Flag only, never auto-remove.** Stripping secrets from git history is a rewrite and needs the user's explicit sign-off — never do this automatically.

**C. Verify `.gitignore` adequacy** — flag if `node_modules`, `.env`, `.DS_Store`, `credentials/`, etc. aren't covered.

## Step 4 — Auto vs. ask

| Action | Auto or ask? |
|---|---|
| Commit/push non-sensitive changes to an **existing** repo with an **existing** remote | Auto |
| Create a **new** GitHub repo (naming, public/private) | Ask first |
| First push of a previously-untracked folder | Ask first |
| Force-push, history rewrite | Ask first, every time |
| Anything flagged as containing a secret | Never auto — flag and stop |

## Step 5 — Linear issue creation

- **Search Linear first** — if an open issue already exists for that repo + issue type, comment/update it instead of creating a duplicate.
- **Project:** a dedicated "Repo Hygiene" Linear project.
- **Title convention:** `[claudec] {folder-name}: {issue}` — e.g. `[claudec] hermes-build: no remote configured`
- **Labels = category, not severity:** `repo-sync`, `secrets`, `needs-remote`, `needs-commit`, `stale`
- **Severity = Linear's native Priority field:** Urgent → secret found in history; Medium → missing remote / unpushed work; Low → stale/dormant repo
- **Description:** which of the six states applies, last commit date, suggested fix.
- **Auto-close:** if a later run finds the issue resolved, close it (or comment) rather than leaving it stale.

## Step 6 — Output

Post a run summary to the Linear "Repo Hygiene" comment thread: projects scanned, states found per project, any secrets flagged, issues created/updated/closed. This is the record that the routine ran, not just chat output.
