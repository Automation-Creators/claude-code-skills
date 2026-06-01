---
name: session-handoff
description: Generate a tight, copy-pasteable handoff summary of the current session so you can /clear and resume in a fresh session without losing context — a faster, controllable alternative to /compact. Captures the goal, what's been done, key files, open decisions, and the exact next step. Use when a session is getting long or sluggish, when context is filling up, when the session has been idle near/past the cache TTL (~1 hour), when switching tasks, or whenever the user asks to "hand off", "wrap up the session", "save context", "summarize so I can clear", or replace /compact.
---

# Session Handoff

Produce a complete, self-contained handoff summary so the user can run `/clear` (starting a fresh, cache-clean session) and paste the summary to pick up exactly where they left off — feeling like nothing was lost.

## Why this exists

`/compact` works but is slow and you don't control what it keeps. A handoff summary is fast (usually under a minute), you decide what carries over, and it pairs perfectly with `/clear` to reset the prompt cache cleanly at a natural task boundary. See the **prompt-cache-optimizer** skill for why resetting at a boundary protects your session limits.

## When to reach for it

- The session is long, sluggish, or context is filling up.
- The session has been idle near or past the cache TTL (~1 hour on a subscription) — resuming cold wastes tokens, so hand off and start fresh instead.
- You're switching to a different task and don't want the old conversation as dead weight.
- You explicitly want a controllable, faster alternative to `/compact`.

## How to produce the handoff

1. **Review the session** — the original goal, decisions made, files created/edited, commands that matter, and what's still open.
2. **Fill in the template below.** Be specific and concrete: real file paths, real command names, real decisions. Omit chit-chat and dead ends (note a dead end only if it prevents repeating a mistake).
3. **Output it as a single fenced code block** so the user can copy it in one action.
4. **Tell the user the next move:** copy the block → `/clear` → paste it → continue. (In Claude Code they can use `/copy` to grab the last message.)

Keep it dense but complete. The test: a fresh session with zero prior context should be able to resume from this summary alone.

## Handoff template

```markdown
# Session Handoff — <short task name>  (<date>)

## Goal
<One or two sentences: what we're ultimately trying to accomplish.>

## Current status
<Where things stand right now, in 2–4 sentences. What works, what doesn't yet.>

## Done so far
- <Concrete accomplishment, e.g. "Added auth middleware in src/auth/mw.ts">
- <…>

## Key files & locations
- `path/to/file` — <what it is / what changed / why it matters>
- <…>

## Important context & decisions
- <Decision made and the reasoning, so it isn't relitigated.>
- <Constraints, conventions, gotchas discovered.>
- <Anything environment-specific: branch name, services running, ports, creds location.>

## Open questions / unresolved
- <Things still undecided or needing the user's input.>

## NEXT STEP (start here)
<The single most concrete next action, specific enough to act on immediately —
e.g. "Wire src/auth/mw.ts into the router in src/server.ts:42 and run `npm test auth`.">
```

## Quality guidelines

- **Specific over vague.** "Refactored the parser" is useless; "Split parseConfig() out of main.go into config/parse.go, tests still pass" is a handoff.
- **Lead the NEXT STEP with a verb and a target.** It should read like a command, not a status update.
- **Preserve hard-won knowledge** — non-obvious constraints, why an approach was rejected, exact reproduction steps for a bug.
- **Don't include secrets** in plaintext; point to where they live instead.
- **Match the session's scale.** A 3-message session needs a 5-line handoff; a 200k-token session needs the full template.
