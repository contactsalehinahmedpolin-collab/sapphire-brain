# Sapphire Lead AI Outreach Team: Team Operating System
Version 1.0 · 9 Nov 2026 · One team, one brain, one lead list. Nobody contacts the same founder twice.

---

## CHECKLIST (how this system was designed)
1. Gave each member exactly one job they're best at, so the members don't overlap.
2. Made the private GitHub repo `sapphire-brain` the only source of truth. Every member reads it and writes only to its own lane.
3. Added a lead-claim rule: a lead belongs to one sender, on one channel, at a time.
4. Set hard limits on volume, timing and channels to protect deliverability and the LinkedIn account.
5. Wrote paste-ready prompts for each member (sections B, C and D).

## SCENARIO SUMMARY
Sapphire Lead (Polin) sells a Growth Engine (funnel, Meta ads, UGC, email flows, CRO) plus factory-direct sourcing from Guangdong to founder-led DTC brands doing $1–20M a year. So far: 74 founders emailed from Gmail, 0 replies, 3 bounces. Ten new cold inboxes are warming up and go live on day 8 (about 16 Nov). The goal is 2 clients a week from day 14. The team is Claude, the Antigravity Telegram bot, Grok, Polin and Shams, all working from the same repo.

---

## A. THE TEAM (one job each)

| Member | Role | Owns | Never does |
|---|---|---|---|
| **Claude** | Head of Research and Strategy | Lead lists (find, verify, score), the personal first lines, email sequences and A/B variants, the CRM, the sync every 4 hours, the review every 3 days, and the follow-ups to the old Gmail batch | Sending from the 10 new inboxes; LinkedIn |
| **Antigravity bot (Telegram)** | Head of Sending and Inbox | Sending from the 10 warmed inboxes (through the sending tool), reply and bounce alerts within 15 minutes, reply drafts for one-tap approval, daily reports to Polin | Writing first emails to new leads itself; sending without a claim; LinkedIn |
| **Grok** | Head of LinkedIn and X Intelligence | For each email lead: find the founder's LinkedIn and X profiles and recent posts, and draft a comment, a connection note and DMs for Polin. Daily signal scan (new launches, funding, retail deals, rebrands) feeding new leads to Claude | Logging in to LinkedIn, auto-sending, scraping, emailing anyone |
| **Polin** | Closer | Approving replies (✅ in Telegram), sending LinkedIn touches by hand (about 45 minutes a day), taking calls, closing | — |
| **Shams** | Quality control | Checking that each A-tier lead is still in their role (Sales Navigator), spot-checking 10 emails a day before send | — |

## B. THE SHARED BRAIN (repo `sapphire-brain`)

| File | Writer | Readers | Purpose |
|---|---|---|---|
| `status.json`, `activity.json` | Claude | All | Live numbers for both senders, CRM stages, the sender switch, caps |
| `leads.json`, `followup_schedule.json` | Claude | All | The old Gmail batch (76 leads) and its follow-ups |
| `pipeline/new_leads.csv` | Claude | Bot, Grok | New verified leads ready to send. Columns: `lead_id, brand, website, niche, first, last, title, email, tz, linkedin_url, fact, fact_source, pain, first_line, variant, owner, status, claimed_at` |
| `pipeline/linkedin_queue.csv` | Grok | Polin, Claude | Per lead: `lead_id, linkedin_url, x_handle, recent_post_url, comment_draft, connect_note, dm1, dm2, dm3, status` |
| `pipeline/signals.csv` | Grok | Claude | New brands with buying signals (launch, funding, retail deal, rebrand, hiring marketing), each with a source URL |
| `sequences/v2.md` | Claude | Bot | The approved email sequence and A/B variants. The bot sends ONLY this copy |
| `outbox/sent.log`, `outbox/events.log` | Bot | Claude | Every send, reply, bounce, approval and pause |
| `inbox/*.md` | Polin (via bot), Grok | Claude | Requests and new ideas |
| `SAPPHIRE_OUTREACH_MAGIC_PROMPT.md` | Claude | All | Voice, rules, ICP, guardrails |

**Lead-claim rule (stops double contact):**
1. Only Claude adds leads to `new_leads.csv`, with `owner=` empty and `status=ready`.
2. Before sending to a lead, the bot sets `owner=bot` and `status=claimed`, then sends, then sets `status=in_sequence`.
3. Grok only adds LinkedIn drafts for leads with `status=in_sequence`. LinkedIn starts after the first email, so the founder sees the name twice.
4. Anyone who sees a reply sets `status=replied`. Every channel stops for that lead, and Polin takes over.
5. A lead is never in two rows. Claude checks for duplicates by email and by domain against everything ever contacted.

## C. LIMITS (written into code, not just rules)
- **New inboxes:** none send cold email before day 8. Then 10 a day per inbox (days 8–10), 15 (days 11–14), and up to 25–30 from day 15, but only while bounces stay under 2% and spam complaints at 0. Pause any inbox whose bounces go over 3%.
- **Old Gmail account:** only follow-ups to the 74, at most 30 a day, until 23 Nov. Then it retires from cold email.
- **Send window:** 9:00–11:00 in the recipient's time zone, Monday to Friday, with Tuesday to Thursday weighted heaviest.
- **Emails:** plain text, no links, 60–90 words, no spam words, asking for a one-word reply.
- **LinkedIn:** Polin sends by hand only, about 15 connection requests a weekday. No bots log in to LinkedIn, ever.
- **Replies:** none are sent without Polin's ✅.

## D. SEQUENCE v2 (the bot sends this from day 8; Claude fills in the variables)
**Email 1 (day 0)**
- Subject A: `<first>, <their thing>` · Subject B: `<2–3 word internal-sounding subject, e.g. "pouch costs">`

```
Hi <First>,

<first_line: one real, sourced fact about them + a sincere observation.>

<pain in one sentence>. I wrote down 3 fixes for <Brand>, including one on <their material/packaging> cost from our factories in Guangdong.

Want me to send them over? Just reply "yes".

Salehin
Sapphire Lead

Not relevant? Reply "stop" and I won't email again.
```

**Email 2 (day 3, in the same thread):**
```
Hi <First>,

One of the three, no strings: <free idea, 2 sentences>.

Want the other two?

Salehin
```

**Email 3 (day 7):**
```
Hi <First>,

Happy to record a 3-minute video walking through <Brand>'s <page/funnel> and what I'd change. Want it?

Salehin
```

**Email 4 (day 12):**
```
Hi <First>,

I'll close your file here. If <topic> moves up the list, just reply and I'll pick it up.

Salehin
```

**When they say "yes":** within 24 hours, Claude writes the 3 fixes and Polin records a Loom. Send both, then: "If useful, happy to walk through it live. Two times that work for you: <slot A> / <slot B>."

## E. DAILY RHYTHM (Dhaka time)
| Time | Who | What |
|---|---|---|
| 06:00 | Grok | Signal scan: new brands go to `signals.csv`; LinkedIn drafts for yesterday's emailed leads go to `linkedin_queue.csv` |
| 08:30 | Bot | Morning brief on Telegram |
| Every 4h | Claude | Sync: CRM ↔ repo, merge the bot's logs, add new leads, dedupe |
| 19:00–23:00 | Bot | Sends inside US morning windows (each inbox spaced, with jitter) |
| 19:30 | Polin | 15 LinkedIn touches from `linkedin_queue.csv` (comment, connect, DM) |
| Live | Bot → Polin | Reply alerts and ✅ approvals |
| 21:00 | Bot | Evening report |
| Every 3 days | Claude | Review: numbers by niche, variant and inbox; 3 changes; report |

## F. TARGETS
- **Days 8–14:** about 750 emails. At about a 2.5% reply rate, that's about 19 replies, about 7 calls and **about 2 clients**.
- **From day 15:** 1,250 or more emails a week. Each week needs about 750–1,250 new verified leads, which means a paid data source plus a verifier.

---

## PROMPT FOR ANTIGRAVITY (add to the master prompt as a new task)
**Context:** You are the team's Head of Sending. The team rules are in SAPPHIRE_TEAM_OS.md in `sapphire-brain`. Ten cold inboxes are warming up in the sending tool; they go live on day 8.
**Task:** Send the v2 sequence to leads in `pipeline/new_leads.csv` through the sending tool, keep claims accurate, alert on replies, and log everything.
**Instruction:**
1. Connect to the sending tool's API (key in an env var) and spread leads across the 10 inboxes evenly.
2. Follow the claim rule in section B exactly.
3. Enforce the caps in section C per inbox per day, in code.
4. Use only the copy in `sequences/v2.md`, filling in variables from the CSV. If any variable is empty, skip that lead and log `skipped missing_var`.
5. Before each send, check: no link, no spam word, lead not `replied` or `stop`, domain not already contacted.
6. Write every event to `outbox/`.
7. Add Telegram commands: `/inboxes` (health, sent today and bounce rate for each inbox), `/pipeline` (counts by status), `/claim <lead_id>` (manual claim).
8. Start in dry-run mode. Go live on Polin's `/golive` after day 8.

**Data:** the repo files in section B, the caps in section C, the sequence in section D.

## PROMPT FOR GROK (paste into the Grok Project instructions, below the master prompt)
**Context:** You are the team's Head of LinkedIn and X Intelligence for Sapphire Lead. Claude owns the email leads; you add the social layer and find new signals. You never log in to or automate LinkedIn. Polin sends everything by hand.
**Task:** Every morning, (1) for each lead whose first email went out yesterday (`status=in_sequence` in `pipeline/new_leads.csv`), produce a LinkedIn row, and (2) find 20 new brands with fresh buying signals.
**Instruction:**
- **LinkedIn row:** `lead_id`, LinkedIn URL (verify the name, company and current role), X handle, one recent post URL (last 30 days), plus:
  - a 2–3 sentence comment that adds insight, with no pitch;
  - a connection note under 200 characters naming one real thing about them;
  - DM1 (thanks, one observation, one question);
  - DM2 (one free idea);
  - DM3 (offer the 3-minute video).
- **Signals:** founder-led brands at $1–20M in pet health, pain & recovery, postpartum, senior care, supplements or sexual wellness, with a launch, funding round, retail deal, rebrand, award or new marketing hire in the last 30 days. Give the source URL for each. Skip brands already in the repo.
- **Rules:** never invent facts or URLs. Mark anything unverified as `HYPOTHESIS`. Write in plain, warm language without hype.

**Data:** the two output files (`linkedin_queue.csv` and `signals.csv` in the formats in section B), plus SAPPHIRE_OUTREACH_MAGIC_PROMPT.md and SAPPHIRE_TEAM_OS.md for voice and rules.
**How it gets in:** Grok can't push to GitHub itself. Polin (or Shams) pastes Grok's output into the Telegram bot with `/grok <paste>`, and the bot commits it to the repo.

## CLAUDE'S STANDING ORDERS (already running)
Every 4 hours: sync the CRM and the repo. Every 3 days: run the review. On request: build lead lists, write first lines, update `sequences/v2.md`, check variants against results.
