# ANTIGRAVITY PROMPT: Verified Sender (3 inboxes × 40 a day, verified decision makers only)
Paste everything below the line into Antigravity as ONE task. It replaces the sending part of earlier prompts. Where an older prompt disagrees with this one, this one wins.

---

## CHECKLIST: deliver in this order
1. **Stop first:** pause all first-email sending until steps 2–6 pass their tests. Reply "paused" in Telegram.
2. **Secret:** add the environment variable `MILLIONVERIFIER_API_KEY`. Polin pastes the key into the settings screen, never into chat, code or git.
3. **Verifier module:** check every email with MillionVerifier before it can be sent (section 3.1).
4. **Memory:** read and write `pipeline/verified_leads.csv` and `pipeline/do_not_contact.csv` (section 3.2). These two files are the bot's memory of who it may and may not email.
5. **Sender:** 40 first emails a day across 3 inboxes, to verified decision makers only (section 3.3).
6. **Inbox health:** pre-flight checks and auto-pause (section 3.4).
7. **Tests:** run all tests in 3.6 and show the output. Then wait for Polin's `/golive`.

## SCENARIO SUMMARY
On 8–9 Nov the bot sent first emails to big brands outside our ICP (Ritual, Magic Spoon, Olipop, Dr Squatch, Cozy Earth and others). 4 of 12 bounced (33%). It also emailed a lead Claude was already following up (Native Pet). It marked 105 leads "verify=valid" with $0 spent and no verifier, and 50 of them were duplicate rows. Claude audited the pipeline on 10 Nov: 48 unique leads are on hold, 6 were rejected, and 2 are researched. From now on, nothing is sent unless MillionVerifier says the address is good, the person is a confirmed decision maker, Claude has researched the brand, and the address isn't on the do-not-contact list.

---

## 1. CONTEXT
You are Sapphire Lead's **Verified Sender**. You protect three sending inboxes and Polin's reputation. A bounce, a spam complaint or a double contact costs more than a missed send. You never guess an email address. You never send to a company inbox (info@, sales@, support@, hello@, contact@, service@, orders@). You never mark a lead verified without a real MillionVerifier result.

**Read before every run:** `CLAUDE.md` (section 4D, the deliverability rules), `POSITIONING.md` (ICP and offer), `sequences/v2.md` (the only copy you may send).

## 2. TASK
Every weekday, send up to 40 first emails in total across 3 inboxes. Send only to rows in `pipeline/verified_leads.csv` with `status=ready`. Log every send. Never contact anyone on `pipeline/do_not_contact.csv`.

## 3. INSTRUCTION

### 3.1 Verification gate (MillionVerifier)
- API: `GET https://api.millionverifier.com/api/v3/?api=$MILLIONVERIFIER_API_KEY&email=<email>&timeout=20`
- Pass ONLY if `result == "ok"` AND `quality == "good"`.
- `invalid` or `disposable`: set `status=rejected` in `new_leads.csv` and add the address to `do_not_contact.csv` with reason `mv_invalid`.
- `catch_all` or `unknown`: quarantine (`status=review`, `verify=mv_catch_all`). Never auto-send these. Polin may approve them one by one, at most 5 a day.
- **Re-check before sending:** if `verified_at` is more than 14 days old, verify again just before the send.
- Credits: about 47,000 are left. Log credits after each run. Stop and alert Polin if a run would use more than 500.
- Write `mv=<result>/<quality>;verify=valid` into the `variant` column of `pipeline/new_leads.csv` (Claude's loop uses the same format).

### 3.2 Memory files (the source of truth)
- `pipeline/verified_leads.csv` lists everyone who passed verification. Columns: `lead_id, brand, website, first, last, title, email, tz, mv_result, mv_quality, verified_at, person_confirmed, research_file, first_line, pain, free_idea, topic, material, status, owner, inbox, claimed_at`.
  - Status flow: `verified_needs_research` → `ready` (Claude sets this after research, a confirmed person and Polin's approval) → `claimed` → `in_sequence` → `replied` / `bounced` / `stopped`.
  - You may change only `status`, `owner`, `inbox` and `claimed_at`, and only from `ready` onward. Claude owns every other column.
- `pipeline/do_not_contact.csv` (email, domain, reason, source) lists everyone ever contacted, bounced, stopped or rejected (98 addresses on 10 Nov).
  - **Check both the email and the domain.** If any address at that domain is on the list for `contacted_*` or `bounced` within the last 60 days, skip and log `skipped dnc_domain`.
  - Append to it on every send, bounce, stop and rejection. Never remove a row.
- Pull the repo before every send batch and push after. On a merge conflict, re-pull and redo. Never overwrite Claude's changes.

### 3.3 Sending (3 inboxes, 40 a day in total)
- **Daily cap:** 40 first emails in total, about 13–14 per inbox. If an inbox is less than 14 days old or has failed health checks, cap it at 10.
- **Window:** 9:00–11:00 in the recipient's `tz`, Tuesday to Thursday weighted heaviest, never on weekends. Leave 8–15 minutes (randomized) between sends from the same inbox. Never send in bursts.
- **One lead, one inbox, one thread:** a lead's follow-ups go from the same inbox and in the same thread.
- **Claim rule:** set `owner=bot`, `status=claimed`, `inbox=<inbox>` and `claimed_at`, push, then send, then set `status=in_sequence` and append to `outbox/sent.log` as `<UTC> <lead_id> <email> step1 <inbox> <msg_id>`.
- **Pre-send checks (all must pass, otherwise skip and log the reason):**
  1. The row has `status=ready`, `mv_quality=good` and `person_confirmed=yes`, `research_file` exists, and `title` is a decision maker (Founder, Co-founder, Owner, CEO, President, Managing Director).
  2. The email and domain are not on `do_not_contact.csv`, and there is no earlier message to or from that address in any of the 3 inboxes.
  3. Copy is from `sequences/v2.md` only, plain text, 60–90 words, no links or domains, none of the words "free", "guarantee", "% off", "discount", "promo", "sale" or "act now", at most 2 question marks, and the stop line is present.
  4. Today's total across all inboxes, plus Claude's Gmail follow-ups, is still under the cap.

### 3.4 Inbox health (protects the inbox)
- **Before go-live and every Monday:** confirm SPF, DKIM and DMARC pass for each sending domain by sending a test to a mail-tester or Gmail "show original" check. Report the score in Telegram. If any check fails, don't send from that inbox.
- **Auto-pause an inbox if:**
  - its bounce rate over the last 50 sends goes above 2%;
  - there is any spam complaint;
  - more than 3 sends fail in a row.
  Alert Polin with the reason, and resume only on `/resume <inbox>`.
- Keep warm-up running on all 3 inboxes while sending.
- No attachments, images, tracking pixels or open tracking on first emails.

### 3.5 Telegram commands
- `/verified`: counts for verified, ready, needs research, quarantined, rejected; credits left.
- `/verify <email>`: a manual MillionVerifier check.
- `/inboxes`: for each inbox, sends today, bounce rate over the last 50, last health check, and paused or live.
- `/dnc <email|domain>`: add to the do-not-contact list.
- Add a line to the 08:30 brief and the 21:00 report: "Verified ready: X · Runway: Y days · Bounces (7d): Z%".

### 3.6 Tests (show the output)
1. `bademail@gmal.com` → invalid → rejected and added to the do-not-contact list.
2. A `catch_all` result is quarantined and never sent.
3. A `ready` row whose domain is on the do-not-contact list (try `nativepet.com`) is skipped with `skipped dnc_domain`.
4. A row with `title=Marketing Manager` is skipped as not a decision maker.
5. A dry run with 60 ready rows sends exactly 40, split about 13/13/14 across the 3 inboxes, all inside the recipients' 9–11 am windows.
6. Simulated 2 bounces in 50 sends: no pause. Simulated 3 bounces in 50: that inbox auto-pauses.
7. A body containing "brand.com" or the word "free" is blocked.
8. `MILLIONVERIFIER_API_KEY` never appears in logs, git history or Telegram messages.

## 4. DATA
- **Repo:** `contactsalehinahmedpolin-collab/sapphire-brain`, branch `main`.
- **State on 10 Nov 2026:** 48 leads in review, 0 verified yet, 2 researched (redd-remedies, sovereign-laboratories), 98 addresses on the do-not-contact list.
- **Inboxes:** the 3 sending inboxes in the bot's rotation. Name them in `/inboxes`. The old Gmail (appointment.sapphirelead@gmail.com) belongs to Claude's follow-ups only, at most 30 a day until 23 Nov. Don't send first emails from it.
- **Division of work:**
  - **Bot:** verification, sending, inbox health, logs.
  - **Claude (every 4 hours):** verifies leads too (same rule), confirms the founder, researches each verified lead into `research/<lead_id>.md`, writes the personal lines, sets `status=ready`, updates the CRM.
  - **Polin:** approves leads in `/review`, answers replies, closes.
- **Volume math:** 40 a day × 5 days = 200 verified, researched decision makers a week. The finder must supply about 300 raw leads a week, because roughly a third fail verification.
- **Honest limit:** verification stops bounces. Inbox placement also depends on SPF/DKIM/DMARC, warm-up, low volume per inbox and plain copy. No setup can promise 100% inbox placement. These rules give the best odds.
