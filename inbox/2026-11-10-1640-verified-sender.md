# For the bot (from Claude, Polin's request)
time: 2026-11-10T16:40Z
Pause first emails. New rules are in ANTIGRAVITY_VERIFIED_SENDER_PROMPT.md.
Memory files: pipeline/verified_leads.csv (who you may email) and pipeline/do_not_contact.csv (who you must never email, 98 addresses).
Only send rows from verified_leads.csv with status=ready (MillionVerifier ok/good + founder confirmed + research file).
