# ANTIGRAVITY PROMPT: "Find 30 more Nature Casts" Lead Engine
Paste everything below the line into Antigravity as ONE task. It adds a lead finder to the bot you already built. It does not replace the team prompt.

---

## CHECKLIST: deliver in this order
1. **Plan:** read everything, then post a 6-line plan.
2. **Build the finder:** a lead-finder module that discovers brands like Nature Cast (section 4), qualifies them, finds the founder, and verifies their email.
3. **First lines:** write a personal first line and a free idea for each lead, using the rules in 3.5.
4. **Output:** write qualified leads to `pipeline/new_leads.csv` in the `sapphire-brain` repo with `status=review`. Polin approves before anything is sent.
5. **Telegram commands:** add `/find <n>`, `/review`, `/approve <id|all>`, `/reject <id> <reason>`.
6. **First run:** produce 30 qualified, verified leads and show them to Polin with `/review`.
7. **Tests:** run the tests in 3.8 and show the results.
8. Ask Polin only about things he alone can do: API keys and spending. For everything else, choose the safe default and say what you chose.

## SCENARIO SUMMARY
Sapphire Lead's first 74 cold emails got 0 replies because they went to big brands with teams and agencies. The new target is founders like **Larry Berman of Nature Cast** (naturecastproducts.com, Coral Springs, FL). Nature Cast is a small, founder-run, direct-response health brand with about 43 products (supplements plus DVD programs), an email list, and a site that still says "©2005–2015". It has no reviews, no subscription, catalog pages split across Shopify subdomains, and a fax order line. It has products and buyers, but its funnel is 10 years old. Brands like this need us, can't build it themselves, and the founder reads his own email. Find 30 of them, verified, with one real reason to write to each.

---

## 1. CONTEXT
You are the team's **Head of Lead Discovery**, working beside Claude (research, writing and CRM) and the sending bot. You think like a direct-response marketer: you look for brands with demand but an outdated way of capturing it. You never guess an email address and never invent a fact.

## 2. TASK
Build and run a repeatable pipeline: discover, qualify, find the decision maker, verify the email, write the first line, then put the lead into `pipeline/new_leads.csv` for Polin's approval. First target: **30 approved leads before 16 Nov**. After that, 100 a week.

## 3. INSTRUCTION

### 3.1 API keys (env vars; Polin provides them; never in code or git)
- `SEARCH_API_KEY`: a web search API (SerpAPI or Brave Search API). This is the main discovery source.
- `EMAIL_FINDER_KEY`: Hunter.io, Apollo or Prospeo, whichever Polin has. If the sending tool (Instantly or Smartlead) has a built-in lead finder or verifier, use that instead.
- `EMAIL_VERIFY_KEY`: MillionVerifier, ZeroBounce or NeverBounce. Required: an email that hasn't been verified never enters the CSV.
- `GITHUB_TOKEN`, `LLM_API_KEY`: already set from the team build.
- **Budget guard:** stop and ask Polin before spending more than $20 in API credits on one run. Log the spend for each run.

### 3.2 Discovery (find brands like Nature Cast)
Run web searches with queries like these, and add your own variations:
- `"natural supplements" "fax" "order line" -amazon`
- `"©2005" OR "©2010" OR "©2012" OR "©2015" supplements "order now"`
- `"dear health enthusiast" OR "dear friend" supplement`
- `"health newsletter" "free daily" supplements catalog`
- `"as heard on" radio supplement "call now"`
- `"infomercial" OR "as seen on TV" health product site:.com`
- `"Authorize.net" supplements "secure order form"`
- `"myshopify.com" supplements OR "pain relief" OR "joint"`
- Niches to cover: supplements, joint and pain relief, men's health, anti-aging, natural remedies, pet supplements, senior health, fitness programs.

Also take brands from `pipeline/signals.csv` (Grok's feed) and any URLs Polin sends with `/find url <link>`.

### 3.3 Qualification (score 0–100; keep 60 and above)
Open the homepage plus 1–2 product pages (respect robots.txt; at most 1 request per second per site; no logins). Score:

| Signal | Points |
|---|---|
| Founder-run or small (a named owner or president, no big team page) | 15 |
| US-based | 10 |
| Sells physical health, pet or wellness products it could source in Asia | 15 |
| Outdated site (old copyright year, non-mobile layout, PHP or old cart, fax line) | 15 |
| No or few reviews or testimonials | 10 |
| No subscription or reorder option | 10 |
| Has an email list, catalog or offline marketing (radio, TV, print), meaning demand exists | 15 |
| Still active (order form works, products listed, updated within the last 2 years) | 10 |

**Disqualify:** big brands (in-house team, 20+ live Meta ads, funding over $10M); sites that are dead or broken; adult products; anything illegal; anything already in `leads.json`, `new_leads.csv` or `outbox/sent.log` (check the domain).

### 3.4 Decision maker and email
1. Find the owner or president on the site (About, Contact, footer, terms or privacy pages), then confirm with one more public source (state business registry, press, podcast or LinkedIn search result). Never log in to or scrape LinkedIn.
2. Get their email from `EMAIL_FINDER_KEY`, or from the site itself if a personal one is published.
3. Verify every email with `EMAIL_VERIFY_KEY`:
   - **valid:** keep.
   - **catch-all:** keep only if there's no better address, and mark `variant=catchall` so Polin sees the risk.
   - **invalid or unknown:** drop.
4. Never use info@, sales@, support@ or orders@ unless the site shows the founder personally answers that inbox. If so, mark `variant=generic` for Polin to decide.

### 3.5 Writing the personal fields (LLM, rules from `SAPPHIRE_OUTREACH_MAGIC_PROMPT.md`)
For each lead, fill:
- `fact`: one specific, true observation from their site, with `fact_source` (URL). Examples: "Your site footer still says ©2005–2015", "18 supplements and no customer reviews on any of them", "orders still go through a fax line".
- `pain`: one plain sentence on what that costs them. Example: "Most buyers check reviews before they order, so a page without them loses the sale to Amazon."
- `first_line`: friendly and specific, 1–2 sentences, no flattery words, no links.
- `free_idea`: one fix they could do this week without us.
- `material`: what their product is made of or packed in (capsules, bottles, DVDs, pouches). This feeds the Guangdong sourcing line.
- `topic`: 2–4 words, used in the subject line and email 3 (e.g. "your order page", "the 2015 footer").
- `niche`, `tz` (from the state), `first`, `last`, `title`.
- **Tone:** respectful. Many of these founders are older operators who built real businesses. Never mock the old site; say it as an opportunity.

### 3.6 Output and approval
- Write rows to `pipeline/new_leads.csv` with `status=review`, `owner` empty, and `lead_id` = slug of the brand.
- `/review` shows 5 leads at a time: brand, founder, email plus verification result, score, fact, first line. Buttons: ✅ Approve / ✏️ Edit / ❌ Reject.
- On approval, set `status=ready`. Only `ready` leads can be claimed and sent, by the existing team rules.
- `/find <n>` runs discovery until n new leads are in review. Report found / qualified / verified / spend.
- Log every run to `outbox/events.log` as `<UTC> finder run found=X qualified=Y verified=Z spend=$N`.

### 3.7 Safety
- Public web pages and paid APIs only. No LinkedIn scraping, no logins, no CAPTCHA bypass, no buying scraped lists.
- Only collect business contact data: name, title, work email, company. No home addresses or phone numbers.
- Every email written must keep the "Not relevant? Reply 'stop'" line.
- Honour stops forever: a stopped domain is blocked from all future runs.

### 3.8 Tests (show the output)
1. Run on naturecastproducts.com itself. Expect: score of 60 or more, founder = Larry Berman (President), fact = the 2005–2015 footer or the fax line, and the email found and verified (or flagged if not found). Mark it `status=review` with a note "reference brand", since Polin decides whether to email it.
2. A big brand (e.g. a $50M+ supplement company) gets disqualified.
3. A domain already in `leads.json` gets rejected as a duplicate.
4. An invalid email gets dropped.
5. `/review`, then `/approve`, moves a row from `review` to `ready`.
6. The budget guard stops the run at $20.

## 4. DATA
**Reference brand (the model for every lead):** Nature Cast (naturecastproducts.com).
- Coral Springs, FL. President: Larry Berman.
- About 18 supplements (e.g. Ayurvedesin, Nature's Life Flow, HGH Activator) and about 25 instructional DVD and fitness programs.
- Direct-to-consumer through its own site, an online catalog and a free daily email list ("Naturecast Alerts").
- Copyright "©2005–2015"; fax order line; payments through Authorize.net; some products on myshopify.com subdomains.
- No reviews, no subscription, no Amazon or retail.

**What we offer these brands (for context; Claude writes the pitch):**
- **The Profit Sprint:** in 30 days, a modern product page and funnel, Meta ads, email and reorder flows, plus a factory-direct quote on their products from Guangdong.
- First the free 3-fix audit; if the Sprint doesn't find value worth more than the first month's fee, they don't pay for month 2.

**Where it goes:**
- Repo `contactsalehinahmedpolin-collab/sapphire-brain`, file `pipeline/new_leads.csv`.
- Header: `lead_id,brand,website,niche,first,last,title,email,tz,linkedin_url,fact,fact_source,pain,material,topic,first_line,free_idea,variant,owner,status,claimed_at,inbox`
- Score and verification result go in `variant`, for example `score=78;verify=valid`. Use `status=review` until Polin approves.

**Team:** Claude reviews approved leads on each 4-hour sync, improves the first lines if needed, and keeps the CRM. The sending bot sends from the 10 inboxes from 16 Nov. Polin approves leads and replies, and closes.
