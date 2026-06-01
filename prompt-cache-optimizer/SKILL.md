---
name: prompt-cache-optimizer
description: Save Claude Code session limits and tokens by maximizing prompt cache hit rate. Explains the 80/20 of prompt caching (cache reads cost 10% of normal input), what silently breaks the cache (model switching, editing the system prompt mid-turn, /compact, idling past the TTL), the cache TTL rules (1 hour on a Claude subscription, 5 minutes on API/subagents), and the three habits that cover 95% of users. Use when the user asks how to save tokens, stretch their session/weekly limits, reduce cost, speed up Claude Code, understand prompt caching or cache hit rate, or asks "what should I do to save tokens?"
---

# Prompt Cache Optimizer

A practical guide to saving tokens and session limits in Claude Code by keeping your **prompt cache hit rate** high. This skill gives you only what you need to know — the 80/20 — not the full nuance of the caching internals.

## The one idea that matters

Every turn, Claude re-reads the entire conversation so far (system prompt + tools + project files like CLAUDE.md + every prior message). **Prompt caching** lets it reuse the unchanged prefix instead of reprocessing it.

- **Cache read** = tokens reused from cache. Costs **~10% of normal input**. This is the win.
- **Cache create (write)** = first-time processing of new content. Normal cost, one-time, pays off on the next turn.

So a day with 91M cached tokens only costs like ~9M of fresh input. A high cache hit rate is why long sessions stay cheap, feel fast, and don't burn your weekly limit. A low hit rate is the opposite — and you usually cause it by accident.

**Your goal: keep the cached prefix intact for as long as possible.**

## How the cache grows (the layered model)

The cached prefix is built in layers, from most stable to most volatile:

| Layer | What's in it | When it breaks |
|-------|-------------|----------------|
| **System** | Base instructions, tool definitions (Read/Write/Bash/Grep/Glob), output style | Switching the model; an update that changes the system prompt |
| **Project** | CLAUDE.md, memory, rules | Editing CLAUDE.md/rules **and** restarting the session |
| **Conversation** | Your messages + Claude's replies | Grows every turn — re-cached each turn, this is normal and fine |

Each turn, only the *new* message + reply has to be written to cache. Everything before it should be a cache read. The danger is invalidating something early in the prefix, because **everything after the break point has to be reprocessed from scratch.** Breaking the cache on turn 2 is cheap; breaking it on turn 16 is expensive.

## The TTL: how long the cache lives

The cache is a snapshot with a **time-to-live (TTL)**. Idle longer than the TTL and the whole snapshot expires — your next message re-creates everything from the beginning.

| Context | Default TTL |
|---------|-------------|
| **Claude Code on a subscription** (terminal / extension) | **1 hour** |
| **Claude API** | **5 minutes** (can be raised to 1 hour for extra cost) |
| **Subagents (any plan)** | **5 minutes** |
| **Over your weekly limit / paying per-token API usage** | **5 minutes** by default |

Note: a month or so back there was community speculation that subscriptions had been secretly dropped to 5 minutes — that turned out to be false. Subscriptions are 1 hour. (Claude.ai web project caching isn't publicly documented in this detail; assume it behaves like the subscription.)

## The three habits that cover 95% of people

1. **Don't pause too long.** If a session has been idle longer than the TTL (≈1 hour on a subscription), the cache is already gone. Hand off to a fresh session instead of resuming a cold one.
2. **Start fresh when you switch tasks.** A new task means the old conversation is dead weight that you re-read every turn. Use `/clear` (or the **session-handoff** skill to carry a summary across). `/compact` also resets the cache.
3. **Keep it focused.** One coherent task per session keeps the prefix stable and the hit rate high.

## What silently breaks the cache (and how to avoid it)

These are the gotchas. Each one forces a re-create of the prefix from the break point on.

- **Switching the model.** Caching is prefix-matched *per model* — each model has its own cache. A `/model` switch means the next request re-reads the entire history with zero cache hits, even though the context is identical.
  - ⚠️ **`opusplan` / "Opus for plan, Sonnet for execution"** counts as a model switch on every plan toggle. It can still save your *weekly limit* by spending Opus budget only on planning, but understand it resets the cache each time it switches — it is not free.
- **Editing the system prompt / output style mid-session.** Changes the most stable layer → everything reprocesses.
- **Editing CLAUDE.md, memory, or rules.** ✅ Safe trick: the edit **doesn't take effect until you restart the session**, so editing CLAUDE.md mid-session does **not** break the live cache. Make your edits now; they apply on the next start.
- **`/compact`.** Rewrites the conversation, so the cache resets. Often worth it to shrink context, but know it's a reset (consider **session-handoff** as a lighter alternative).
- **Idling past the TTL.** Covered above.

## "What should I do to save tokens?" — quick decision guide

- **Session idle > ~1 hour?** → Don't resume cold. Run **session-handoff**, `/clear`, paste the summary, continue.
- **Switching to an unrelated task?** → `/clear` (or **session-handoff** first). Don't pile a new task onto an old conversation.
- **Tempted to switch models mid-session?** → Only do it at a natural break point; expect a one-time full re-create. Avoid frequent toggling.
- **Want to tweak CLAUDE.md/rules?** → Edit freely mid-session; it applies on next start and won't break the current cache.
- **Context getting huge / sluggish?** → Prefer **session-handoff** → `/clear` over `/compact` (faster, and you control what's kept). Both reset the cache, so do it at a task boundary.
- **On the API or using subagents?** → Remember the 5-minute TTL. Batch your turns; don't let it sit. Raise TTL to 1 hour only if the extra cost is worth it.
- **Want visibility?** → Track input / output / cache-create / cache-read tokens (see below). A healthy long session shows cache reads dwarfing fresh input.

## Reading your own numbers

When you can see token stats, the four numbers to watch:

- **Input tokens** — fresh input processed at full price.
- **Output tokens** — what Claude generates.
- **Cache create** — first-time writes into the cache (one-time cost).
- **Cache read** — tokens reused at ~10% cost. **This is the savings.**

Healthy sign: on a long, focused session, daily **cache read** is many multiples of **input**. If cache read is low relative to input, something is breaking your prefix — re-check the list above (most often: model switching or resuming cold sessions).

## TL;DR

Keep it alive (don't idle past the TTL), keep it focused (one task per session), and start fresh when you switch (`/clear` or session-handoff). Don't switch models mid-task, and edit CLAUDE.md freely since it only applies on restart. Do that and your cache hit rate — and your session limits — take care of themselves.
