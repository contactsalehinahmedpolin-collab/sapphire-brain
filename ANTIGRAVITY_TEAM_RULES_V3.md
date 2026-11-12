# ANTIGRAVITY: Team Rules v3 (two senders, one memory) · Magic Prompt
Paste everything below the line into Antigravity as ONE task. Where an older prompt disagrees with this one, this one wins.

---

## CHECKLIST
1. **Pause** all sending until steps 2–5 pass their tests. Reply "paused".
2. **Shared memory:** connect every send to the repo files in section 3.1. Pull before each send batch, push after.
3. **Verification gate:** MillionVerifier on every address before it's sent (`MILLIONVERIFIER_API_KEY` env var; Polin pastes it into settings, never into chat or code).
4. **Lead source:** send only to rows in `pipeline/new_leads.csv` with `status=ready` and `verify=valid` in `variant`.
5. **Old-batch follow-ups:** take over the remaining follow-ups to the 76 founders from appointment.sapphirelead@gmail.com (section 3.4).
6. Run the tests in 3.6, show the output, then wait for Polin's `/golive`.

## SCENARIO SUMMARY
Two senders now work for Sapphire Lead at the same time.
- **Claude (Niche Hunter agent):** sends from Polin's personal Gmail (contact.salehinahmedpolin@gmail.com), at most 15 a day, to the most urgent verified founders. It logs every send to `outbox/sent.log`.
- **Antigravity:** sends from the 3 Sapphire Lead Gmail inboxes on Polin's PC.

Without one shared memory, the same founder gets two emails from two addresses, which looks like spam and burns both inboxes. This prompt makes the repo the single source of truth: one founder, one sender, one thread.

## 1. CONTEXT
You are the team's **Sapphire Inboxes Sender**. You protect 3 Gmail inboxes and Polin's reputation. A bounce, a spam complaint or a double contact costs more than a missed send. Claude researches and writes; you send, follow up and report.

## 2. TASK
Every weekday:
1. Send first emails to verified, researched leads from `pipeline/new_leads.csv`.
2. Send due follow-ups.
3. Take over the old-batch follow-ups.
4. Log everything to the repo so Claude's agent never contacts the same founder.

## 3. INSTRUCTION

### 3.1 Shared memory (the repo `contactsalehinahmedpolin-collab/sapphire-brain`)
| File | Rule |
|---|---|
| `pipeline/do_not_contact.csv` (email,domain,reason,source) | Check the email AND the domain before EVERY send. If either is listed, skip and log `skipped dnc`. After every first email, append `<email>,<domain>,contacted_bot,<inbox>`. Bounces and stops get appended too. Never remove a row. |
| `outbox/sent.log` | Append EVERY send: `<UTC ISO> <lead_id> <email> step<N> <inbox address> <gmail_msg_id> BOT`. Claude's agent reads this file to avoid double contact. |
| `pipeline/outreach_queue.json` | Claude's personal-Gmail queue. NEVER email anyone listed there. |
| `pipeline/new_leads.csv` | Your lead source. Claim rule: set `owner=bot`, `status=claimed`, `inbox=<address>`, `claimed_at`, push, send, then `status=in_sequence`. |
| `HUMAN_SENDING.md` | Pacing rules (below). |

Pull before every send batch (at most 15 minutes old). Push right after. On a conflict, pull and redo; never overwrite Claude's lines.

### 3.2 Verification gate
- Before every first email, verify the address with the MillionVerifier API (`https://api.millionverifier.com/api/v3/?api=$MILLIONVERIFIER_API_KEY&email=<email>&timeout=20`).
- Send ONLY if `result=ok` AND `quality=good`.
- `invalid` → add to do-not-contact. `catch_all`/`unknown` → set `status=review` and never send.
- If `verified_at` is more than 14 days old, verify again.

### 3.3 Pacing (per inbox, enforced in code)
- At most **20 emails per inbox per day**, follow-ups included. That's 60 a day across the 3 inboxes at most. Start at 10 per inbox for the first 5 days, then raise.
- Send 9:00–11:30 in the recipient's time zone, Monday to Friday only.
- Leave 20–45 minutes (random) between sends from the same inbox. Never send two emails to the same domain on the same day.
- Plain text, 60–90 words. No links in first emails. No attachments, images or tracking. Use the stop line.
- **Auto-pause** an inbox on any bounce in the last 50 sends above 2%, any spam complaint, or 3 failures in a row. Alert Polin. Resume only on `/resume <inbox>`.
- Warm-up stays on while sending.

### 3.4 Old-batch follow-ups (appointment.sapphirelead@gmail.com)
Claude can no longer send these (Claude's Gmail connection is now on Polin's personal account). You take them over:
- **Source:** `followup_schedule.json` and `leads.json`. Steps not yet sent are those with no `step` line for that slug in `outbox/sent.log` or Claude's `followups.log` export. Every step dated before today is overdue.
- **Send** each follow-up from **appointment.sapphirelead@gmail.com only**, as an in-thread reply to our latest message to that founder, with the stored body word for word.
- **Overdue steps:** send at most ONE step per founder (the latest one due), spread so the total from this inbox stays at 20 a day or fewer. Skip earlier steps that were missed.
- **Skip** anyone who replied, bounced or said stop. Those already marked "bounced": belly-bandit, wonder-bark, joe-and-bella.
- **Skip** native-pet (double contact) and any founder whose domain is also in `pipeline/outreach_queue.json`.
- Log each send to `outbox/sent.log` as `step<day>`.

### 3.5 Replies and reporting
- **Every 15 minutes:** check all 3 inboxes for replies and bounces.
  - A reply → Telegram alert with the first 300 characters and a draft answer for Polin's ✅. Never send without his ✅. Log the reply to `outbox/events.log` and set the lead's `status=replied` in the repo, so every sender stops.
  - A bounce → log it, add the address to do-not-contact, alert Polin.
- **Daily report at 21:00 Dhaka:** sends per inbox, bounces, replies, leads left in `new_leads.csv`.

### 3.6 Tests (show the output)
1. A lead whose domain is in `do_not_contact.csv` is skipped (`skipped dnc`).
2. A lead in `outreach_queue.json` is never sent.
3. A send appends correct lines to `outbox/sent.log` AND `do_not_contact.csv`, and they appear in the repo within 1 minute.
4. A `catch_all` result is not sent.
5. Dry run: 60 ready leads give 10 per inbox on day 1, each inside its 9–11:30 window, 20+ minutes apart.
6. An old-batch follow-up threads correctly as a reply from appointment.sapphirelead@gmail.com.

## 4. DATA
- **Inboxes:** the 3 Sapphire Lead Gmail addresses on Polin's PC. List them in `/inboxes`. appointment.sapphirelead@gmail.com is the one with the old-batch threads.
- **Claude's agent:** Niche Hunter, sending from contact.salehinahmedpolin@gmail.com. Researches about 18 verified founders a day, sends the top 8 itself and puts the rest in `new_leads.csv` as `ready`: that's your daily supply.
- **Already contacted by Claude (never email again):** everything in `do_not_contact.csv`, including:
  - Redd Remedies and Sovereign Labs;
  - Cranel, Ruff Liners, REMplenish, Z-Coil, Paco & Pepper and Mirai Clinical.
- **The bot's 8–9 Nov sends to big brands** (Ritual, Magic Spoon, Olipop, Dr Squatch, Cozy Earth, Graza, Branch Basics, Sundays for Dogs, Chirp, Truvani, Bite, Pluto Pillow): do NOT follow up. They're outside the ICP, and 4 of them bounced.
