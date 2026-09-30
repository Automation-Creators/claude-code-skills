---
name: offer-engine
description: Design and A/B test risk-reversed front-end offers for cold email, DMs, landing pages and lead magnets. Scores an offer against the three traits (finished, borderline free, theirs to keep) and four parts (deliverable, speed, risk reversal, proof), drafts one variant per offer family (book-a-call control, diagnosis/audit, finished deliverable, pay-on-result, beat-it guarantee), and sets up a one-variable reply-rate test with sample sizes and kill rules. Use when the user wants to write, fix, compare or test an offer, asks why cold outreach isn't getting replies, or mentions "offer", "front-end offer", "risk reversal", "lead magnet offer", "audit offer", "pay on result", or "guarantee".
---

# Offer Engine

Build the first thing a stranger is asked to say yes to, then **test it** instead of trusting it.

## Status of the underlying claims — say this to the user

The framework comes from a third-party infographic (GrowthFlare, "the cold email offer engine",
"built off 20 million sent emails") whose charts are all labeled *illustrative, not measured*, plus
Alex Hormozi's offer and pricing videos. Both sell services built on the idea. Treat every claim as a
hypothesis to test on the user's own list. Never present the illustrative reply rates as data.

## The core claim

> The offer decides whether a stranger replies, not the subject line, the script, or the list. Hand
> them a finished, valuable thing up front and carry all the risk yourself.

Illustrative ranking (lowest → highest reply): "book a call" < free audit (a diagnosis) < finished
deliverable done for them < pay only on the result.

## Step 1 — Gather (ask only what's missing)

1. Who is the buyer (vertical, size, the 1–2 facts that predict they can pay)?
2. What does the user sell on the back end, at what price?
3. What is the current front-end offer, word for word?
4. What can the user deliver in ≤72 hours for free, and how many hours does one take?
5. Channel (cold email, LinkedIn DM, landing page, ads) and list size available.

If the user has a project config (e.g. a `theme.yaml`, ICP doc, offer docs), read it instead of asking.

## Step 2 — Score the current offer (0/1 each, rewrite below 5/7)

| Check | Question |
|---|---|
| Finished | Can they use it the day it arrives with no further work? (An audit or "recommendations" = homework = 0) |
| Borderline free | Is the cost of yes ~0? No card, no required call to receive it? |
| Theirs to keep | Do they keep it even if they never buy? |
| Specific deliverable | Is it a countable noun ("10 ad creatives", "one agent that sends your Monday report")? |
| Hard timeline | Is there a number of hours or days that sounds almost too fast? |
| Risk reversal | Is it free, pay-on-result, or beat-it-or-nothing-changes, with the downside visibly the seller's? |
| Proof built in | Is it compared against their current best so the gap shows on open? |

Two pass/fail gates (Hormozi):
- **Qualifier:** the user checks the 1–2 predictive facts before spending delivery hours.
- **Back end priced for the buyer with money:** the thing it feeds is worth 5–10x more, and isn't a
  mid-priced SMB retainer (Hormozi's claimed churn "dead zone" is ~$1.5–3K/month with 4–6 month average
  stick).

## Step 3 — Draft one variant per family

Always include a control. Fill all four parts for each.

| Family | Lever | Template |
|---|---|---|
| Control | none | "Worth a 20-min call to see how I'd [outcome]?" |
| Diagnosis | free insight | "I'll [audit/review] your [thing] and send you the doc." |
| Finished deliverable | done-for-them | "I'll build [specific thing] for your [business], live in [N] hours, tested against your current [thing]. Keep it either way." |
| Pay on result | pay-on-result | "[Deliverable] free. You pay only if [measurable result], and only [% / fee] of what it [saves/recovers]." |
| Beat-it guarantee | risk-off | "If it doesn't beat your current [rate/package/number], nothing changes and you owe nothing." |

Reference patterns by family (examples from the source, adapt the structure, never copy):
- *Finished result:* 10 UGC creatives tested against their best ad, live in 72h, delete if they lose; abandoned-cart flow rebuilt and live in 48h; 3 vetted candidates in 5 days, pay only if you hire.
- *Paid on result:* freight refunds recovered, paid from what comes back; property tax, paid only if the bill drops; dead-lead reactivation, free 7-day campaign, pay on closed deals.
- *Risk-off:* AI agent turns RFQs into sent quotes in 24h, free 30-day trial; voice AI paid per call it saves; beat your benefits package or change nothing.

Add to every variant **one damaging-admission line** (Hormozi: an offer that sounds too good is
believed more when you say who it won't work for): "Not for you if …".

Keep each cold-email variant ≤90 words with exactly one CTA. Don't invent proof: if the user has no
case study, the proof slot is "tested against your current [X]", not a fabricated result.

## Step 4 — Set up the test

- **One variable:** same list segment, sender, subject, opener, send window. Only the offer + CTA change.
- **Randomize:** shuffle the segment and deal round-robin into arms.
- **Size:** cold reply rates are low. Per arm, to separate 2% from 8% takes ~200 sends; 1% vs 5%
  ~285; 2% vs 6% ~375 (alpha 0.05, power 0.8). Default: 3 arms × 200 sends per round. At this n, a tie
  means "not a 3–4x gap," not "no difference."
- **Cap delivery:** finished-deliverable and pay-on-result arms cost hours. Qualify first; cap builds
  per round; log hours per build.
- **Metrics per arm:** sends, replies, positive replies, qualified, delivered, delivery hours, calls,
  $ closed. Primary = positive-reply rate. Decision = $ closed per 100 sends once any arm closes.
- **Rules:** KEEP an arm that beats control ≥2x on positive-reply rate at ≥200 sends; KILL at 200
  sends with ≤1 positive reply; before a free-build offer becomes the default, require ≥1 paid
  back-end sale per 5 builds, or it's a free-work machine.
- **Round 2** tests framing on the winner (plain-free vs pay-on-result vs + damaging admission).

## Step 5 — Output

Produce: (1) the scorecard for the current offer, (2) the variant table with all four parts filled,
(3) the round plan with arm sizes and dates, (4) an empty results table with the metric columns above.
If the project has a tracker file, append to it instead of creating a new one.

## Guardrails

- Drafts only. Never send, DM, or post on the user's behalf without explicit sign-off.
- Never build a cold list by scraping commenters or anything behind a login; list sourcing follows the
  platform's terms.
- Never present the source's illustrative numbers, or Hormozi's, as the user's expected results.
- Pay-on-result and guarantee offers in regulated areas (lending, insurance, medical billing, tax)
  need the user to confirm they're licensed and compliant before any draft is written.
