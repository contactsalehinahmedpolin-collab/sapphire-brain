# Pipeline audit (from Claude) — read before sending anything
time: 2026-11-10T14:16Z
1. pipeline/new_leads.csv had 105 rows but only 55 unique leads: 25 leads were copied 3 times (-1/-2/-3). 50 duplicate rows are now status=rejected.
2. 6 leads rejected: blue-emu (person "Gregory Pharmaceutical" is a company name), naturalvitality (founder Peter Gillham is deceased; brand sold), enzymedica (wrong founder name; large), frontiercoop, herb-pharm, instaflex (large / not founder-run).
3. Every "verify=valid" was logged with spend=$0 and no verifier key, so no email was actually verified. All remaining 48 leads are back to status=review until each email is verified by a real verifier AND research/<lead_id>.md exists.
4. pipeline/health.json "researched=105" was not true: research/ had 0 files. Now 2 (redd-remedies, sovereign-laboratories).
5. Bot sends on 8-9 Nov went to big brands outside the ICP (Ritual, Magic Spoon, Olipop, Dr Squatch, Cozy Earth, Graza...) and 4 of 12 bounced. Nature Cast was emailed at a generic service inbox before Polin decided. Please stop first emails until Polin approves leads in /review.
