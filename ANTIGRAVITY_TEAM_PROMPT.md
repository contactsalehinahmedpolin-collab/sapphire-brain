# ANTIGRAVITY TEAM PROMPT: Sapphire Lead AI Outreach Team (one prompt, all members in sync)
Attach `sapphire-brain-seed.zip` and `SAPPHIRE_TEAM_OS.md`, then paste everything below the line as ONE task.
This replaces every earlier Antigravity prompt.

---

## CHECKLIST: build in this order, and don't stop until every test in section 3.10 passes
1. **Plan:** read the whole prompt, then post an 8-line plan.
2. **Repo:** make sure the PRIVATE repo `contactsalehinahmedpolin-collab/sapphire-brain` exists. If it doesn't, create it. Unzip the seed into it, add `SAPPHIRE_TEAM_OS.md`, create `pipeline/new_leads.csv`, `pipeline/linkedin_queue.csv`, `pipeline/signals.csv` and `sequences/v2.md` (headers and copy are in section 4), and push to `main`.
3. **Claude's access:** walk Polin through giving the Claude GitHub app access to this repo: GitHub → Settings → Applications → Installed GitHub Apps → Claude → Configure → Repository access → add `sapphire-brain` → Save. Ask him to confirm with a screenshot.
4. **Secrets:** guide Polin through each secret in 3.1, one at a time. Store them only in env vars or a git-ignored `.env`.
5. **Build:** extend the existing Telegram bot with everything in 3.2–3.8.
6. **Tests:** run every test in 3.10 and show the output.
7. **Always on:** deploy it to run 24/7 (3.9). Tell Polin the monthly cost before deploying.
8. **Handover:** write a one-page plain-English README, and give Polin the exact message for Claude: "repo access added, bot live".
9. Ask Polin only about things he alone can do (logins, approvals, payments). For everything else, choose the safe default and say what you chose.

## SCENARIO SUMMARY
Sapphire Lead (founder Salehin Ahmed Polin, "Polin", Asia/Dhaka, UTC+6) sells a Growth Engine (funnel, Meta ads, UGC, email flows, CRO) plus factory-direct sourcing from Guangdong to founder-led US DTC brands doing $1–20M. So far: 74 founders emailed from Gmail, 0 replies, 3 bounced. Ten new cold inboxes are warming up in a sending tool and go live around 16 Nov. Goal: 2 clients a week from day 14.

The work is done by an AI team that shares ONE brain, the GitHub repo `sapphire-brain`:
- **Claude** researches and writes, and runs the CRM.
- **This bot** sends, watches replies and reports.
- **Grok** drafts LinkedIn and X touches and finds new brands.
- **Polin** approves and closes; **Shams** checks quality.

Every member reads the repo and writes only in its own lane, so everyone sees the same numbers and no founder is ever contacted twice.

---

## 1. CONTEXT
You are the world-class AI automation engineer building and running the team's **Head of Sending and Inbox**: Polin's Telegram control room. Priorities, in order: protect real prospects and the sending domains, reliability, simplicity, then features. Polin is non-technical, so explain every step plainly.

**Team roles (full detail in `SAPPHIRE_TEAM_OS.md`):**

| Member | Owns | Never does |
|---|---|---|
| Claude | Lead lists, verification, first lines, sequences, CRM, a repo sync every 4h, a review every 3 days, follow-ups to the old Gmail batch | Sending from the 10 new inboxes; LinkedIn |
| This bot | Sending through the 10 inboxes, the claim rule, reply and bounce alerts within 15 min, reply drafts for ✅ approval, daily reports, committing Grok's output | Writing new first emails itself, sending without a claim, LinkedIn |
| Grok | LinkedIn and X drafts per emailed lead; new brands with buying signals | Logging in to or automating LinkedIn |
| Polin | ✅ approvals, LinkedIn by hand, calls, closing | — |
| Shams | Role checks, spot-checking emails | — |

**The shared brain (repo `sapphire-brain`, branch `main`):**

| File | Writer | Purpose |
|---|---|---|
| `status.json`, `activity.json` | Claude | Live numbers for all senders, CRM stages, `sender` switch (for the old Gmail batch), caps |
| `leads.json`, `followup_schedule.json` | Claude | The old Gmail batch (76 leads) and its follow-ups |
| `pipeline/new_leads.csv` | Claude adds rows; the bot updates `owner`, `status`, `claimed_at`, `inbox` | New verified leads for the 10 inboxes |
| `pipeline/linkedin_queue.csv` | Grok (committed by the bot) | LinkedIn and X drafts for Polin |
| `pipeline/signals.csv` | Grok (committed by the bot) | New brands with buying signals for Claude to research |
| `sequences/v2.md` | Claude | The ONLY email copy the bot may send |
| `outbox/sent.log`, `outbox/events.log` | The bot | Every send, reply, bounce, approval, pause and skip |
| `inbox/*.md` | The bot (from Polin or Grok) | Requests to Claude |
| `SAPPHIRE_OUTREACH_MAGIC_PROMPT.md`, `SAPPHIRE_TEAM_OS.md` | Claude | Voice, rules, ICP, team rules |

## 2. TASK
Build, test and deploy the bot so the whole team runs in sync:
1. Send the v2 sequence through the 10 inboxes with strict claims and caps.
2. Alert Polin to replies and bounces within 15 minutes, with a reply draft he can approve in one tap.
3. Give Polin live team status from the repo.
4. Pass requests and Grok's work into the repo.
5. Log everything, so Claude's 4-hour sync keeps the CRM complete.

## 3. INSTRUCTION

### 3.1 Secrets (env vars only, never in code, git or chat)
- `TELEGRAM_BOT_TOKEN`
- `TELEGRAM_OWNER_CHAT_ID`: capture it on Polin's first /start and ignore every other chat. Optional: `TELEGRAM_TEAM_CHAT_IDS` for Shams, read-only.
- `GITHUB_TOKEN`: fine-grained, `sapphire-brain` only, Contents read/write.
- `SENDER_TOOL` (instantly | smartlead | smtp) and `SENDER_API_KEY`. If `smtp`: `SMTP_ACCOUNTS_JSON`, a list of 10 `{email, host, port, user, app_password, imap_host}` entries. Ask Polin which tool holds the 10 inboxes.
- `GMAIL_CLIENT_ID`, `GMAIL_CLIENT_SECRET`, `GMAIL_REFRESH_TOKEN`: for appointment.sapphirelead@gmail.com, read and watch only (Claude sends the old batch).
- `LLM_API_KEY`
- `DRY_RUN=true`, `TZ_OWNER=Asia/Dhaka`, `LAUNCH_DATE=2026-11-16`

### 3.2 Repo access
- Use the GitHub REST contents API: GET to read, PUT with the file's `sha` to write. On a 409 conflict, re-read, re-apply your change and retry, up to 3 times.
- For CSV edits, change only your own columns in the specific row, and keep every other byte intact.
- Cache reads for at most 2 minutes. Show the `updated` time with any numbers.
- If `status.json.updated` is more than 6h old: "⚠️ Claude sync is late".

### 3.3 The claim rule (prevents double contact; enforce it in code)
1. A lead can be sent only if `status == ready` and `owner` is empty.
2. Claim before sending: set `owner=bot`, `status=claimed`, `claimed_at=<UTC>`, `inbox=<address>`, and commit.
3. After the first email is accepted by the tool, set `status=in_sequence`.
4. On a reply, set `status=replied`. On a "stop" reply, set `stop`. On a bounce, set `bounced`. Every channel stops for that lead.
5. Before claiming, refuse if the lead's email or domain appears anywhere in `leads.json`, `outbox/sent.log` or another `new_leads.csv` row that is already past `ready`.

### 3.4 Sending (the 10 inboxes)
- **Before `LAUNCH_DATE`:** no cold sends at all. Warm-up only.
- **Daily cap per inbox:** 10 on days 1–3 after launch, 15 on days 4–7, then 25. Raise to 30 only if that inbox's bounce rate (last 7 days) is under 2% and spam complaints are 0. Auto-pause any inbox above 3% bounces, and alert Polin.
- **Send window:** 09:00–11:00 in the lead's `tz`, Monday to Friday. Space sends from each inbox 8–15 minutes apart with random jitter. Spread leads evenly across inboxes.
- **Copy:** use only `sequences/v2.md`, filling variables from the CSV (`first`, `brand`, `first_line`, `pain`, `material`, `topic`, `variant`). If any variable is missing, skip and log `skipped missing_var`.
- **Pre-send checks:** no `http`, `www.` or `word.tld` in the body; none of the words free, % off, discount, guarantee, hurry, offer, sales, promo, cheap; at most 2 question marks; lead not replied, stopped or bounced.
- **Sequence:** email 1 on day 0, then days 3, 7 and 12, in the same thread. Stop on any reply.
- **Old Gmail batch:** the bot never sends it while `status.json.sender == "CLAUDE"`.
- Log every send to `outbox/sent.log` as `<UTC> <lead_id> <email> <step> <inbox> <message_id>`.

### 3.5 Replies (the most important feature)
- **Every 15 min:** check all 10 inboxes plus appointment.sapphirelead@gmail.com for messages from any lead in `new_leads.csv` or `leads.json`, and for bounces.
- **On a reply:**
  - Alert Polin: `🔥 REPLY: <First> at <Brand> (<inbox>): "<first 300 chars>"`.
  - Log it to `events.log` and update the lead's status.
  - Draft an answer using `SAPPHIRE_OUTREACH_MAGIC_PROMPT.md`. If they said "yes", the draft says the 3 fixes are coming within 24h, and the bot writes `inbox/<date>-yes-<lead_id>.md` so Claude writes the fixes. Otherwise: answer in 2–3 sentences and offer 2 times in their time zone.
  - Buttons: ✅ Send / ✏️ Edit / ❌ Skip. Send only on ✅, from the same inbox and in the same thread.
- **On a bounce:** `⚠️ BOUNCE`, log it, set the lead to `bounced`, and count it toward that inbox's bounce rate.

### 3.6 Telegram commands (owner only; the team chat gets the read-only ones)
- `/status`: team summary from `status.json`, `activity.json` and the pipeline counts.
- `/activity`: sends by Claude (old Gmail batch) and by the bot (10 inboxes), today and in total, plus the last 5 actions on each side.
- `/pipeline`: `new_leads.csv` counts by status and by niche.
- `/inboxes`: per inbox, sent today, cap, 7-day bounce rate, and whether it's active or paused.
- `/today`: today's planned sends and follow-ups, with local times.
- `/lead <brand|id>`: the lead's card and its history.
- `/replies`: replies and bounces from the last 7 days.
- `/ask <text>`: writes an `inbox/` file for Claude.
- `/grok <pasted Grok output>`: parses the CSV rows. LinkedIn rows go to `pipeline/linkedin_queue.csv`; signal rows go to `pipeline/signals.csv`. Reject rows without a source URL. Then confirm the counts.
- `/linkedin`: today's 15 LinkedIn touches from `linkedin_queue.csv`, as copy-ready messages. Polin marks each one done with a button, which updates its status.
- `/pause <lead|inbox>`, `/resume <inbox>`
- `/switch bot|claude`: the old Gmail batch sender. Asks for confirmation, then edits only `status.json.sender`.
- `/golive` and `/dryrun`, each with a confirmation.
- `/help`
- **Free text:** answer from repo data using the rules file. If the message is a request for work, offer `/ask`.

### 3.7 Scheduled jobs (Dhaka time)
- **Every 15 min:** reply and bounce watch (3.5).
- **Every 10 min, 19:00–03:00:** run the sending loop for leads whose local window is open (3.4).
- **08:30:** morning brief. Overnight replies and bounces, today's plan, inbox health, and the size of the LinkedIn queue.
- **21:30:** evening report. Sent today by inbox and by Claude, replies, skips with reasons, bounce rate, and tomorrow's plan.
- **Hourly:** health check. If repo writes or the sending tool fail twice in a row, pause sending and alert Polin.

### 3.8 Code quality
- Modules: `repo`, `claims`, `sender`, `watcher`, `writer`, `bot`, `scheduler`.
- Timezone-aware datetimes (zoneinfo), retries with backoff, structured logs.
- Unit tests for claims, caps, the send window, the link and spam-word filter, and CSV round-trips.

### 3.9 Always on
Deploy to the cheapest reliable 24/7 option (for example Railway or Render), with auto-restart and a Telegram alert if the bot is down for more than 30 minutes. Show Polin the monthly cost first.

### 3.10 Tests (show the output for each)
1. The repo is seeded. `/status` shows 74 delivered, 3 bounced (belly-bandit, wonder-bark, joe-and-bella), 0 replies.
2. `/ask test` creates an `inbox/` file.
3. `/grok` with 2 sample rows adds them to the right CSVs. A row without a source URL is rejected.
4. Dry run: add 3 fake leads (to Polin's own test addresses) to `new_leads.csv`. The bot claims, builds the emails, respects the window and caps, and sends nothing in dry-run mode.
5. Duplicate guard: a fake lead whose domain is already in `leads.json` is refused.
6. The filter blocks a body containing "brand.com" or "free".
7. Before `LAUNCH_DATE`, a forced live send is refused.
8. A simulated reply triggers the alert, the draft and the ✅ buttons. On "yes", the `inbox/` file for Claude is created.
9. Inbox auto-pause triggers when bounces are simulated above 3%.

## 4. DATA

**`pipeline/new_leads.csv` header:**
```
lead_id,brand,website,niche,first,last,title,email,tz,linkedin_url,fact,fact_source,pain,material,topic,first_line,variant,owner,status,claimed_at,inbox
```

**`pipeline/linkedin_queue.csv` header:**
```
lead_id,linkedin_url,x_handle,recent_post_url,comment_draft,connect_note,dm1,dm2,dm3,status
```

**`pipeline/signals.csv` header:**
```
brand,website,niche,founder,signal,signal_date,source_url,notes,status
```

**`sequences/v2.md`** (copy exactly; `{variables}` come from the CSV):
```
## Email 1 (day 0)
Subject A: {first}, {topic}
Subject B: {topic}

Hi {first},

{first_line}

{pain} I wrote down 3 fixes for {brand}, including one on {material} cost from our factories in Guangdong.

Want me to send them over? Just reply "yes".

Salehin
Sapphire Lead

Not relevant? Reply "stop" and I won't email again.

## Email 2 (day 3, same thread)
Hi {first},

One of the three, no strings: {free_idea}

Want the other two?

Salehin

## Email 3 (day 7, same thread)
Hi {first},

Happy to record a 3-minute video walking through {brand}'s {topic} and what I'd change. Want it?

Salehin

## Email 4 (day 12, same thread)
Hi {first},

I'll close your file here. If {topic} moves up the list, just reply and I'll pick it up.

Salehin
```
Add `free_idea` as a CSV column after `first_line`.

**Current state (9 Nov 2026):**
- Old Gmail batch: 76 leads, 74 delivered, 3 bounced, 0 replies. Follow-ups run 9–23 Nov from appointment.sapphirelead@gmail.com, sent by Claude (`sender=CLAUDE`).
- 10 new inboxes: warming up, launch 16 Nov.
- Targets: about 750 emails in week 1 after launch, about 2 clients a week from day 14.

**Rhythms:**
- Claude: syncs the repo and CRM every 4h; reviews every 3 days (reads `outbox/` and `inbox/`, adds new leads, updates `status.json` and `activity.json`).
- Grok: produces drafts and signals at 06:00 Dhaka; Polin pastes them with `/grok`.
- Polin: 15 LinkedIn touches at 19:30 Dhaka.

**Owner:** Salehin Ahmed Polin. GitHub: contactsalehinahmedpolin-collab.
