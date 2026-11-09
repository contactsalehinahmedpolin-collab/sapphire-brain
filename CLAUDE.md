# 👑 SAPPHIRE BRAIN: MASTER OPERATING SYSTEM (CLAUDE.md)
**Universal Bootloader for Sapphire Lead & Rayat Shared Production System**  
*Read this file first before executing any task in this repository.*

---

## 1. WHO WE ARE & WHAT WE SELL
- **Agency:** Sapphire Lead (Plattsburgh, NY & Guangdong, China). Founder & CEO: Salehin Ahmed Polin ("Polin").
- **The Core Value Engine ("The Full Loop"):**
  1. **Growth Engine ($5,000/mo retainer / $1,500 Margin Audit):** Funnel design, mobile drawers, CRO, advertorials, Meta ads, UGC/VSL creative, automated 30/60-day email/SMS replenishment flows.
  2. **Guangdong Sourcing Wedge:** Direct quotes from our Guangdong manufacturing network on bottles, blister cards, pumps, dropper pipettes, pouches, and capsule filling.
  3. **The Lock:** Slashing 30% to 50% off unit packaging COGS completely pays for the $5,000/mo marketing retainer out of net margin savings.
- **Mindset Trifecta:**
  - **Alex Hormozi:** Lead with margin and unit economics; eliminate downside risk; make offers irrational to refuse.
  - **Naval Ravikant:** High-status founder authority; play long-term games; never sound needy or desperate.
  - **David Ogilvy:** Specifics beat cleverness. Factual research, real customer language, zero fluff.

---

## 2. SYSTEM TOPOLOGY: SHARED BRAIN, TWO NODES

```
                      ┌────────────────────────────────┐
                      │    contactsalehinahmedpolin-   │
                      │      collab/sapphire-brain     │
                      └───────────────┬────────────────┘
                                      │
              ┌───────────────────────┴───────────────────────┐
              ▼                                               ▼
┌───────────────────────────────┐               ┌───────────────────────────────┐
│     NODE A: CLOUD BRAIN       │               │     NODE B: RAYAT'S PC        │
│   (Polin / Claude / Antigravity)│             │       (Local Heavy Stack)     │
├───────────────────────────────┤               ├───────────────────────────────┤
│ • Lead Sourcing & Clay Enrich │               │ • Local SMTP Jitter Pacer(A1) │
│ • Polin Voice & Teardowns     │               │ • ffmpeg Transcoding & Stitch │
│ • Higgsfield & Kling 3.0 Pro  │               │ • Whisper Lip-Sync / Audits   │
│ • Taplio/Typefully LinkedIn   │               │ • CapCut Desktop MCP Post     │
│ • Tally Forms & Calendly Ingest│              │ • High-Res Video File Storage │
│ • Meta Data Client Reporting  │               │ • Direct Client Klaviyo Store │
└───────────────────────────────┘               └───────────────────────────────┘
```

---

## 3. THE 12-AUTOMATION MATRIX & ROLES
- **A1: Cold Email Engine (SMTP):** Runs on Rayat's PC (local jitter & reply-stop). Fed strictly with Polin's teardown copy from `pipeline/new_leads.csv`.
- **A2: Outreach Campaign 3 (114 leads):** Re-qualify against Oct 30 ICP (pain relief, pet, women's health, senior, sexual wellness, legacy offline brands).
- **A3: LinkedIn Automation:** Managed here via Taplio & Typefully on Polin's profile. Composio retired.
- **A4: Universal UGC Studio:** Workspace template standardized in `sapphire-brain`.
- **A5: UGC Production:** Prompts & Kling 3.0 Pro renders here (Higgsfield Max wallet); ffmpeg & Whisper on Rayat's PC.
- **A6: Static Ad Factory:** Creative Standard v2 applied universally across both workstations.
- **A7: Video Edit Pipeline:** Python EDL (`make_edl.py`) and ffmpeg stay on Rayat's PC. Skill backed up to `skills/video-edit`.
- **A8: CapCut Automation:** Desktop MCP runs locally on Rayat's machine.
- **A9: Reel Watcher:** Parked (Mac only); re-home to Polin's Mac when needed.
- **A10: Harvey Sales Agent:** **RETIRED.** Merged into A1 + unified outbound engine to prevent double contact.
- **A11: Klaviyo Flows:** Runs on Rayat's PC via direct store access.
- **A12: Client Reports:** Built here from exported Meta Ads Manager data using the Rock-ettes template.

---

## 4. STRICT GUARDRAILS (ZERO-TOLERANCE RULES)

### A. Regulatory & Meta Compliance (Rock-ettes & NOX47)
- **NO "LAST LONGER" CLAIMS:** The ad account is *"Nox47 Re-activate"*, indicating past bans. Never use *"last longer"*, *"cure"*, *"treat"*, or medical promises.
- **Approved Angles:** *Bedroom stamina, nighttime confidence, youthful vitality, couples' harmony, botanical blood flow support*.
- **Mandatory FDA Disclaimer:** Must appear on all landing pages and ad cards.

### B. Guarantee Lock
- **30-Day Guarantee Only:** All scripts, bridge pages, and ad copies must match the live Shopify 30-day policy. Purge all 60-day references.

### C. Creative & Video Rules
- **Ask Before Paid:** Always quote duration, Kling 3.0 Pro credits, and wallet balance before firing paid jobs.
- **Kling 3.0 Pro Exclusively:** No low-tier plastic AI video generators.
- **Zero Real Faces:** Synthetic personas only. Log prompt, negative prompt, and seed in `SCENE_MANIFEST.md`.
- **Text & Audio in Post:** Post-processing (CapCut/ffmpeg) adds captions and music—never rely on raw AI text hallucinations.

### D. Outbound Deliverability Fortress
- **Zero Links:** No URLs or tracked redirects in cold emails 1 & 2.
- **Plain Text Only:** Grade-6 readability, 60–90 words, asking for a one-word reply (*"Want me to send them over? Just reply 'yes'"*).
- **Lead-Claim Rule:** Only send leads with `status=ready` and empty `owner`. Set `owner=bot`, `status=claimed` before sending.
- **Verification Gate:** MillionVerifier check MUST return `result=ok` and `quality=good`. Catch-alls and stale domains are quarantined.

---

## 5. ESSENTIAL REPOSITORY SCHEMAS
- Master Leads Queue: `pipeline/new_leads.csv`
- Live State & Counts: `status.json` and `activity.json`
- Approved Email Sequence: `sequences/v2.md`
- Immutable Audit Logs: `outbox/sent.log` and `outbox/events.log`
- Strategy Operating System: `POSITIONING_OS.md`
- Team Protocol: `SAPPHIRE_TEAM_OS.md`
