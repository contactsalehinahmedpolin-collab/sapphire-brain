# ANTIGRAVITY PROMPT: Pipeline Loop (3 days of leads always ready, researched before send)
Paste everything below the line into Antigravity as ONE task. It upgrades the finder and sender you already have. It replaces nothing.

---

## CHECKLIST: deliver in this order
1. **Plan:** read this prompt plus `POSITIONING.md`, `ANTIGRAVITY_LEADFINDER_PROMPT.md` and `research/README.md` in the repo, then post a 5-line plan.
2. **Floor job:** keep 120–150 sendable leads in `pipeline/new_leads.csv` at all times (3 days × 40 a day).
3. **Research gate:** the sender only sends to rows whose `variant` contains `research=done`.
4. **Daily cap:** 40 first emails a day across the 10 inboxes from 16 Nov (ramp per the team caps).
5. **Telegram:** add `/pipeline`, `/research <lead_id>`, `/refill`.
6. **Tests:** run the tests in 3.6 and show the output.
7. Ask Polin only about API keys and spending. Choose the safe default for everything else and say what you chose.

## SCENARIO SUMMARY
Sapphire Lead sends 40 first emails a day to founders of small, founder-run DTC health, pet and wellness brands like Nature Cast. Searching for leads on the day of sending is too late: research gets skipped and emails go generic. So the pipeline must always hold 3 days of leads (120 minimum, 150 target). While the bot sends today's 40, Claude researches the next ones (what's broken on their site and funnel right now, what it costs them, and our 3 fixes) and writes it to `research/<lead_id>.md`. The CRM shows every lead from "queued" to "won", so Polin tracks everything in one place.

---

## 1. CONTEXT
You are the team's **Pipeline Manager**. You own volume: lead discovery and sending. Claude owns depth: research, first lines and the CRM. You never send to a lead that Claude has not researched, and you never let the pipeline fall below 3 days of sends.

**Read `POSITIONING.md` before every run.** It holds the ICP, the offer (the Profit Sprint), the proof we're allowed to state and the voice. If it changes, your filters and copy change with it.

## 2. TASK
Run this loop every day without being asked:
1. **Morning (07:00 Dhaka):** count sendable leads (status `review` or `ready`). If the count is under 120, run `/find <150 − count>` automatically, within the $20 budget guard.
2. **All day:** Claude researches leads every 4 hours and marks them `research=done`. You don't touch research files.
3. **Approval:** Polin approves in `/review`. Researched leads show first, with a one-line summary of the research.
4. **Send window (19:00–23:00 Dhaka):** send up to 40 first emails, only to `status=ready` rows with `research=done`, oldest first. Follow the claim rule.
5. **Night (23:30 Dhaka):** log the day to `outbox/events.log` and post the pipeline report.

## 3. INSTRUCTION

### 3.1 Pipeline floor (in code)
- `sendable = rows where status in (review, ready)`; `researched = sendable rows with research=done`.
- Floor: 120. Target: 150. If `sendable < 120`: refill. If `researched < 40` at 18:00 Dhaka: alert Polin "⚠️ Only N researched leads for tonight", and write `inbox/<date>-research-short.md` so Claude picks it up.
- If the finder can't reach the floor (no API key, budget hit, few results), say why in Telegram. Never fill the gap with unverified emails or company inboxes.

### 3.2 The research gate
- Before claiming a lead, check that `research/<lead_id>.md` exists and `variant` contains `research=done`. If not, skip it and log `skipped no_research`.
- If the research file has a `Watch-outs` line starting `WRONG FIT`, or the status is `rejected`, never send.
- Use the row's `first_line`, `pain` and `free_idea` exactly as Claude wrote them. Claude may rewrite them after research; always read the latest row just before sending.

### 3.3 Sending
- 40 first emails a day in total, split across the inboxes that are live. Keep the per-inbox caps from `SAPPHIRE_TEAM_OS.md` section C (10 a day each on days 8–10, 15 on days 11–14). The tighter limit always wins.
- Send window: 9:00–11:00 in the recipient's time zone, Monday to Friday. 8–15 minutes between sends from the same inbox, with jitter.
- Copy: `sequences/v2.md` only. Plain text, no links, the one-word "yes" ask, and the stop line.
- After each send: `status=in_sequence`, a line in `outbox/sent.log` (`<UTC> <lead_id> <email> step1 <inbox> <msg_id>`).

### 3.4 Telegram commands (owner only)
- `/pipeline`: sendable, researched, ready, in_sequence, replied, rejected; days of runway (`researched ÷ 40`); the last finder run (found / verified / spend).
- `/research <lead_id>`: the research file's "What's broken right now" and "Our 3 fixes" in under 12 lines.
- `/refill`: run the finder now up to 150.
- Add the runway to the 08:30 brief and the 21:00 report: "Runway: X days (Y researched leads)".

### 3.5 Safety
- Everything from the team prompt still applies: one sender per lead, no replies without Polin's ✅, no LinkedIn automation, no secrets in git or chat, stops honoured forever.
- The budget guard ($20 per run) applies to automatic refills too. At most 2 automatic refills a day.

### 3.6 Tests (show the output)
1. With 0 rows in the pipeline, the morning job asks the finder for 150 (or reports exactly why it can't).
2. A `ready` row without `research=done` is skipped with `skipped no_research`.
3. A row marked `rejected` after research is never sent.
4. The daily send stops at 40 even with 150 ready rows.
5. `/pipeline` shows runway in days and matches the CSV.
6. Changing a row's `first_line` just before the send window means the new line is the one sent (dry run).

## 4. DATA
- **Repo:** `contactsalehinahmedpolin-collab/sapphire-brain`, branch `main`.
- **Files:** `POSITIONING.md` (read first), `pipeline/new_leads.csv`, `research/<lead_id>.md`, `research/README.md` (the research format), `sequences/v2.md`, `outbox/sent.log`, `outbox/events.log`, `inbox/`.
- **CSV header:** `lead_id,brand,website,niche,first,last,title,email,tz,linkedin_url,fact,fact_source,pain,material,topic,first_line,free_idea,variant,owner,status,claimed_at,inbox`.
- **`variant` tags:** `score=78;verify=valid;research=done`. Other values: `verify=catchall`, `generic`.
- **Status flow:** `review` → (Polin approves) `ready` → (claimed) `claimed` → (sent) `in_sequence` → `replied` / `bounced` / `stopped` / `rejected`.
- **Volume:** 40 first emails a day from 16 Nov = 200 a week = about 800 verified leads a month. The finder needs a search API plus an email finder and verifier to do that. Without them, the floor can't be met.
- **Claude's side:** every 4 hours Claude syncs the CRM, researches up to 12 leads into `research/`, rewrites weak first lines, mirrors every pipeline row into the CRM as "queued", and writes `pipeline/health.json`.
