# Human sending rules (protects Polin's inboxes)
Set 12 Nov 2026 at Polin's request: "send in a humanised manner so my email is protected".

Applies to every email sent from contact.salehinahmedpolin@gmail.com (personal) and appointment.sapphirelead@gmail.com.

1. **Volume.** At most 15 emails a day in total from the personal Gmail (first emails + follow-ups), of which at most 8 first emails; at most 20 from the Sapphire Gmail. No sending on weekends.
2. **Timing.** Send at 9:00–11:30 in the recipient's time zone, never on the hour. Wait 20–45 minutes (random) between sends from the same inbox. No bursts.
3. **Only verified addresses.** MillionVerifier result=ok AND quality=good (Make tool "Verify email with Million Verifier"). Catch-all, unknown or invalid addresses are never sent.
4. **Plain like a person.**
   - One recipient per email, no CC or BCC.
   - No links, images, attachments or tracking.
   - 50–90 words, lowercase personal subject.
   - Every email unique to the founder.
5. **Same thread.** Follow-ups go as replies in the original thread, every 3–4 days, 3 touches maximum, then stop.
6. **Stop rules.**
   - A reply, a "stop" or an out-of-office from someone else stops that lead.
   - One bounce pauses the inbox for 24 hours and starts a check.
   - Two bounces in a week cut the daily cap in half.
7. **Do-not-contact.** Check `pipeline/do_not_contact.csv` (email AND domain) before every send. Add every sent address to it.
8. **Log.** Every send goes in `outbox/sent.log` with the inbox used.
