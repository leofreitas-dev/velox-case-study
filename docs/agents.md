# The AI agents and rule robots

[Back to the README](../README.md)

As of Nov 14, 2026, Velox runs **26 AI agents and 30 rule robots** in 24-hour shifts.

- An **AI agent** is a role with its own instructions that calls a language model (Claude).
- A **rule robot** runs fixed rules in code. It never calls a language model.

All numbers in the "Work to date" columns are as of Nov 14, 2026. The database does not keep everything from the start (early test accounts were deleted), so read them as "at least".

Models are Claude Haiku 5.5, Sonnet 5.5 and Opus 5.5 unless stated. "Medium", "high" and "low" are the effort settings. "Escalates" means: if the first model refuses, returns a broken answer or fails the validator, the task goes once to the bigger model.

**Contents**

- [Seller's stores](#sellers-stores)
- [Newsroom](#newsroom-velox-notícias)
- [Instagram](#instagram)
- [WhatsApp and billing](#whatsapp-and-billing)
- [Website](#website)
- [Growth floor (Meta Ads)](#growth-floor-meta-ads)
- [Rule robots](#rule-robots)
- [Not running yet](#not-running-yet)

---

## Seller's stores

The seller sees 13 employees on the team screen. **Only 5 of them use AI.** 6 are rule robots (see [Rule robots](#rule-robots)) and 2 are still in training (see [Not running yet](#not-running-yet)).

Rules that apply to all store agents:

- The team's shift runs at the end of every store read. If the subscription has expired, the shift does not run.
- Automatic mode only turns on with the terms accepted. The Shipping Watcher, the Listing Expert (titles) and Customer Service each need one more acceptance of their own.
- Default cap: 30 automatic actions per store per day. The store picks from 1 to 500. Specs and titles have their own daily numbers, outside this cap.
- During the free trial, Multiply, Recovery, Shipping and Questions stay in "ask for approval".
- If an ERP manages the store, Multiply, Recovery and Catalog are forced to "ask for approval". Specs are held only when the ERP controls specs, and weak titles only when it controls titles. Shipping is not affected.
- Default plan limits: 30 new AI listings, 30 spec sets and 10 titles per client per day; 50 answers per store per day; R$ 120 of AI per month. During the free trial: R$ 15 of AI for the whole trial.

| Agent | What it does | What triggers it | Modes | Model | Work to date |
|---|---|---|---|---|---|
| **Listing Expert** | Scores every listing from 0 to 100, completes the technical specs and improves weak titles. The score is plain code; specs and titles use AI | Every read shift. Specs: in automatic mode up to the daily number (default 50, from 1 to 500); in approval mode up to 5 cards per shift. Titles: up to 5 per shift; in automatic mode 5 a day by default (from 1 to 50). Fixes: up to 5 serious listings per shift | Score: always. Specs: approval (default), automatic or off. Titles: approval (default), automatic with its own acceptance, or off | Haiku 5.5 medium, escalates to Sonnet 5.5 | 366 activity records; 70 actions proposed (11 done, 10 resolved, 49 pending); 25 spec sets (2 applied, 2 ready, 20 discarded, 1 failed to apply); 25 spec calls and 11 title calls |
| **Recovery agent** | Builds a new listing with AI to replace one that Mercado Livre took down | Every read shift; a notice that an item was just blocked; a request on screen | Approval (default), automatic or off | Haiku 5.5 high, escalates to Sonnet 5.5 | 1 activity record; 2 actions (1 done, 1 resolved); 1 rebuilt listing published |
| **Lister** | Creates listings with AI from the client's spreadsheet, and multiplies best sellers (Multiply) | Multiply: every read shift, when a listing sells 2 in 24 hours or 5 in 7 days (defaults); rounds of 2 copies, 7 days apart; or a request on screen. Spreadsheet: queue every 5 s, the day's release every 30 min, products and photos every 15 s, Google Drive every hour | Multiply: off (default), approval or automatic. Spreadsheet: approval (default), automatic or off | Haiku 5.5 high, escalates to Sonnet 5.5 | 98 activity records (part of them are the Copier's quick copies); 41 Multiply drafts (33 published, 6 ready, 2 failed) |
| **Copier** | Puts a listing on the official catalog page through the official path, and makes copies of listings | Catalog: every read shift (approval: up to 3 cards per shift; automatic: up to 5 a day). Copies: a request on screen | Catalog: off (default), approval or automatic. Copy on request: no mode | Titles for copies: Haiku 5.5 medium, escalates to Sonnet 5.5. Copy from a link uses the listing route. Catalog: no AI | 41 activity records; 3 copy drafts (1 ready, 2 failed) |
| **Customer Service** (with the store's Brain) | Writes the answer to each buyer question from the listing, the store's Brain and past answers. The Brain takes facts from sent answers, for the store to confirm | A question notification from Mercado Livre; a sweep every 3 min; automatic answers checked every 15 s, after a 3-minute wait; a question waiting more than 1 hour becomes a notice | Approval (default), automatic with its own acceptance, or off. In automatic mode only "confident" answers go out, up to the daily number (default 50) | Answers: Haiku 5.5 high, escalates to Sonnet 5.5 (no escalation when not confident). Brain: Haiku 5.5 low | 4 activity records; 29 questions read, 3 answered by Velox; 11 answer calls |

The Recovery agent, the Lister and the Copier share one AI route for listings: 49 calls in total.

---

## Newsroom (Velox Notícias)

All three are internal. A mission runs in steps. Before each step, the engine checks the brake and the dollar cap. The routine runs every 60 seconds on the server.

| Agent | What it does | What triggers it | Modes | Model | Work to date |
|---|---|---|---|---|---|
| **Marketplace Researcher** | Builds 3 story ideas from recent news and picks one, saying why. Each idea needs 2 sources. Without 2 new items, the mission stops before calling the AI | Right after the sources are read. The first mission at the set time (default 7:30), the next ones every 2 hours until 19:00, within the missions-per-day setting (0 to 8). In use: 6 a day | No mode of its own. Missions per day = 0 turns the newsroom off. Brake; cap of US$ 0.10 a day and US$ 0.05 per mission | Haiku 5.5 medium, escalates to Sonnet 5.5 | 82 calls; 93 missions |
| **SEO Writer** | Writes the original article, 600 to 1,200 words, and stores a versioned proposal with a hash. It does not publish | A chosen story, or a proposal sent back | No mode of its own. Brake; cap of US$ 0.80 a day and US$ 0.40 per mission | Haiku 5.5 high, escalates to Sonnet 5.5 | 81 proposals; 115 calls |
| **Editorial Supervisor** | The judge. Objective checks in code, then an AI review that holds each claim against the sources. Approves, returns, rejects or asks for a human decision. At most 3 returns | A new proposal | Depends on the publisher's autonomy: the founder decides in the admin panel, or the supervisor decides. Since Oct 29, 2026, the supervisor decides | Sonnet 5.5 medium, no escalation. If it and the writer were ever the same model, the approval turns into a human decision | 81 reviews (43 approve, 21 return, 15 reject, 2 human decision); 77 calls. The founder approved 6 in the admin panel |

Result as of Nov 14, 2026: 93 missions. 28 done, 10 still being measured, 49 rejected by the process, 6 failed. 38 articles live.

---

## Instagram

All five are internal. Shared rules:

- The production routine runs every 10 minutes, with at most 1 post per run, up to 9 productions a day, a daily AI cap (US$ 5 in use; default US$ 4) and at least 4 hours between posts.
- **No human approval.** A post that passes the checks and the council goes straight to the queue as approved.
- Default grid: Monday to Friday, with slots at 8:00, 12:00 (carousel) and 18:30 (Reel, only when Reels are on).
- Velox does not make Instagram stories.

| Agent | What it does | What triggers it | Modes | Model | Work to date |
|---|---|---|---|---|---|
| **Scriptwriter** (carousels and Reels) | Writes the slides, caption, hashtags, alt text and sources, or the Reel script. Rewrites what the checks or the council send back. Does not publish | The 10-minute routine, when there is a free slot | Off or automatic; AI through the API or the desktop worker; Reels on or off. Obeys the brake | Opus 5.5 medium, no escalation | 28 productions (US$ 7.49 of AI); 11 posts published (9 carousels and 2 Reels); 2 Reels cancelled; last run on Nov 6 |
| **Social Supervisor** (chairs the sales council for Instagram) | Takes the post to the council's 9 seats, which vote in one call. Code makes the decision. It never writes posts | A new or rewritten post that passed the checks | Same as the Scriptwriter. Its brake also stops the routine | Sonnet 5.5 medium, no escalation | 60 calls |
| **Hook Hunter** | Keeps a living library of hook types for Reels. Code updates the scoreboard (promotes or retires types, no AI); the AI brings up to 2 new types a day | Checked every 30 min; runs once a day from 7:00 | Off or automatic; API or desktop worker. Honest limit: it does not read the operations brake | Opus 5.5 medium | 15 rows in the library, 10 of them from the base seeded by code; last run on Nov 6 |
| **Reels Director** | Reads the Reels of the last 3 weeks and writes the goal for the next one, up to 6 lessons for the Scriptwriter and a reference Reel. It does not change the template | Checked every 30 min; runs once a day from 22:00 | Off or automatic; API or desktop worker. Honest limit: no brake or dollar-cap check in the code | Opus 5.5 medium | Last run on Nov 5 |
| **Weekly Analysis** (n8n workflow) | Writes the week's summary that the Scriptwriter reads | Every Monday at 7:00 | n8n workflow | Calls the Anthropic API directly from n8n with Claude Sonnet 5.5, outside the app's cost log | 2 runs stored |

**The council's 9 seats** vote approve, adjust or reject, and can raise vetoes. They do not decide and do not rewrite: code decides.

| Bench | Seats |
|---|---|
| Person | Psychology; Buyer journey; Adult learning |
| Conversation | Listening and rapport; SPIN; Triggers; Persuasion and objections |
| Facts | Product; Market and competition |

The last Instagram post and production before a pause are from Nov 9, 2026. A setting saved with extra quotes stopped the routine; it was fixed on Nov 14 and the routine was switched back on on Nov 15.

---

## WhatsApp and billing

| Agent | What it does | What triggers it | Modes | Model | Work to date |
|---|---|---|---|---|---|
| **João** (talks to leads and clients on WhatsApp) | Sales and support on WhatsApp: answers whoever writes, releases the free trial, receives the Pix receipt and answers clients after reading their account in the app. His knowledge base is a set of Google Docs in a Drive folder | Every incoming message, through the CRM workflow's webhook | No approval step; runs whenever the workflow is on. Above 40 messages per number per day, n8n stops calling the AI for that number and alerts the founder. "Stop" turns off automatic messages. It is outside the admin brake | n8n agent with Claude Sonnet 5 (not 5.5). His AI cost is not in the app's cost log | 158 CRM workflow runs (Oct 31 to Nov 14) |
| **Pix Receipt Reader** (internal) | The AI only transcribes the receipt. Code decides: Pix completed, plan amount, Velox as receiver, date within 48 hours, ID in the Central Bank's format. It never creates access | João's workflow calls it when a receipt arrives. Up to 5 reads per phone number every 10 minutes | None | Opus 5.5 low, no escalation | 3 calls |

---

## Website

| Agent | What it does | What triggers it | Modes | Model | Work to date |
|---|---|---|---|---|---|
| **Website Assistant** (talks to site visitors) | Chats with the visitor in the site's chat bubble and leads to the free trial. It does not store the conversation, does not ask for personal data, does not promise results and does not claim to be a person | Each visitor message. Up to 16 messages of up to 800 characters; 20 messages per IP every 10 minutes | None. It turns off with a cap of 0 or without the AI key. Default cap: US$ 3 a day | Haiku (medium), escalates to Sonnet; through the API | 11 calls |

---

## Growth floor (Meta Ads)

Ten internal agents that work on Velox's own paid acquisition. Shared rules:

- The floor's loop runs every 10 minutes, one process at a time. The order is: funnel meter, Meta reading, treasury, campaign executor, then the AI rounds.
- By default the AI answers through the desktop Claude worker. If no answer comes in 48 hours, the round fails.
- **Modes:** there is no off / approval / automatic on this floor. There is the admin brake (no new rounds) and a weekly cap on rounds (default 30, at most 80).
- In every prompt: no role executes anything outside the system, and the QA never judges its own material.
- **Only one rule robot, the Campaign Executor, talks to Meta.** It only accepts proposals from the Paid Media Manager, with a creative the QA approved.
- Models: work jobs on Haiku 5.5 high (escalates to Sonnet 5.5 if it fails); direction jobs on Sonnet 5.5 medium; reviews on Opus 5.5 low.
- Jobs are counted as ready / blocked / archived / queued.

Status as of Nov 14, 2026: the founder paused the campaigns on Nov 9. The floor keeps measuring. The Executor has no Meta token, so nothing goes out to Meta.

| Agent | What it does | What triggers it | Model | Work to date |
|---|---|---|---|---|
| **Maestro** (Growth Director) | Turns the funnel, the cash and the integrations into a ranked queue, with at most one acquisition experiment and one activation experiment. It never proposes media actions | Monday to Saturday from 9:05, one round a day; plus requests from other roles | Direction | Jobs 3 / 9 / 13 / 1 |
| **Market and Competition Intelligence** | Finds sellers' pains, objections and words, and what competitors show in public, always with a source | Tuesday and Friday from 10:10; plus requests from the Maestro | Work | Jobs 4 / 0 / 0 / 0 |
| **Positioning and Brand** | Builds the message matrix: audience, available proof, and the claims blocked for lack of proof | Day 1 of each month from 10:30; plus requests | Direction | Jobs 2 / 0 / 0 / 0 |
| **Ad Copywriter** | Writes 3 to 5 ad variations per hypothesis, inside the code's limits | Only on request, up to 5 rounds a day | Work | Jobs 8 / 0 / 7 / 0 |
| **Reels and Ad Scriptwriter** | Writes video scripts of 20 to 35 seconds | Only on request, up to 5 rounds a day | Work | Jobs 11 / 1 / 9 / 0 |
| **Truth Reviewer (QA)** | The independent judge of each creative: fit, fix or blocked | A request in the queue or a pending creative; up to 6 rounds a day, up to 8 creatives per round | Review | Jobs 16 / 0 / 10 / 0; 16 calls; 19 fit, 12 fix |
| **Paid Media Manager** | Looks after the live campaign and proposes actions to the Executor. It is the only author the Executor accepts | Monday to Saturday from 11:10, up to 3 rounds a day | Direction | Jobs 9 / 5 / 8 / 0; Meta confirmed 6 ads created and 6 paused |
| **Data and Attribution Analyst** | Reads the funnel with its denominator and limits. Logs up to 5 learnings per round, only as hypothesis, inconclusive or rejected ("confirmed" needs 10 paying clients) | Monday and Thursday from 8:20 | Work | Jobs 3 / 3 / 2 / 0 |
| **Learning Curator** | Keeps the operation's versioned memory, with a review date 30 days ahead | Friday from 16:10 | Work | Jobs 2 / 0 / 0 / 0 |
| **Activation and Conversion** | Finds where interested people stop and proposes a short experiment. It never sends messages or touches a client's account | Wednesday from 15:10; plus requests from the Maestro or Data | Work | Jobs 4 / 0 / 1 / 0 |

Floor totals as of Nov 14, 2026: 84 rounds from Nov 3 to Nov 14; 31 creatives from the Copywriter and the Scriptwriter (QA: 19 fit, 12 fix); 12 actions confirmed by Meta from the Paid Media Manager.

---

## Rule robots

Rule robots never call a language model.

### Seller's stores (visible to the seller)

| Robot | What it does |
|---|---|
| **Sentinel** | Reads the store's listings and warns when Mercado Livre takes a listing down, or when a Multiply copy goes offline. It never changes a listing. 315 activity records |
| **Finance** | Calculates the real profit of each listing (commission, shipping, cost), flags what sells at a loss and calculates the price that reaches the minimum margin. It never changes a price by itself. 358 activity records; 24 actions proposed |
| **Strategist** | Writes the store's plan for the day, with the number of actions waiting and how many are urgent. At most once a day per store. 75 activity records |
| **Shipping Watcher** | Notices when the free-shipping cost goes up and calculates the price that keeps the margin. In automatic mode it raises the price by at most 15% at a time (default), and only if shipping went up by at least R$ 1 per sale. It never lowers a price. 34 activity records; 3 actions proposed |
| **Reporter** | Closes each store's daily report: what the team did, what sold and what is waiting for the seller. 147 activity records; 147 daily reports |
| **Stock Keeper** | Links the stock of an original listing and its copies (in groups it only lowers stock), and runs the central stock (in automatic mode, it pauses listings at zero and levels the listings). 116 activity records; 190 stock writes to Mercado Livre since Oct 14 |

### Newsroom

| Robot | What it does |
|---|---|
| **Source Curator** | Reads only the registered and allowed feeds, with GET requests, respecting robots.txt. Stores title, link, date and a short summary. Each source at most every 170 minutes |
| **Publisher** | Publishes exactly the approved version, checking the hash. From 8:00 to 20:00, at most 6 a day, 2 hours apart |
| **Content Analyst** | Measures each article at 24 hours and at 7 days: visits, clicks on the trial invite and the rate. No cookies, no stored IP |
| **System** (mission engine) | Moves each mission through valid states and checks hash, brake, cap and duplicates. The author never judges its own proposal |

### WhatsApp and billing

| Robot | What it does |
|---|---|
| **Trial Guide** | João's messages along the free trial: 7 types, each once per client. Text built by the app, no AI. 12 messages sent |
| **Subscription Reminders** | A notice 2 days before the due date and after it is overdue. Text from the app, no AI |

### Operations

| Robot | What it does |
|---|---|
| **Funnel Meter** | Counts the day's funnel and reads performance on Meta. It only stores totals |
| **Treasury** | Calculates the spend that can be authorized, blocks all spend if one of the 5 financial fields is missing, and pauses Velox's campaigns when the month's spend reaches the media cap |
| **Campaign Executor** | The only one that talks to Meta. It can only pause, resume, adjust a budget or create an ad, and only with a proposal from the Paid Media Manager and a creative the QA approved. It has a simulation mode and an operation mode, and switches between them on its own |
| **On-call** (8 watchers) | Finds failures that cost money or clients before the client does. It only reads the database, opens and closes alerts and tells the founder on WhatsApp. It never fixes anything |

The on-call robot's 8 watchers:

1. An AI request to the desktop worker without an answer for more than 30 minutes.
2. The last read of an active store failed in the last 24 hours.
3. An answer with a send error, and a question without an answer for more than 30 minutes.
4. 3 or more AI calls with an error in 6 hours.
5. The same listing repriced upward 3 or more times in 7 days.
6. A charge overdue for more than 5 days and still open.
7. Mercado Livre access expired for more than 15 minutes.
8. A store in good standing with no read for more than 12 hours.

### Routines without a name in the registry

These also run on fixed rules and are part of the 30.

| Routine | What it does |
|---|---|
| Newsroom art studio | Draws each article's art in HTML and captures it with a headless Chrome, in 8 variants. Nothing comes from the internet |
| News alerts | Each article published in the last 6 hours becomes an e-mail to confirmed subscribers. 28 alerts sent |
| Search engine notice | Tells IndexNow about each new article |
| Instagram Publish (n8n) | Takes the approved post whose time has come and publishes it. No AI node |
| Instagram Radar (n8n) | Reads public posts of reference accounts (807 posts from 16 accounts). No AI node |
| Instagram Metrics (n8n) | Reads the Instagram metrics. No AI node |
| Site watcher | Checks rules R1 to R6 every 15 minutes and sends at most 3 alerts a day. It is the source of 26 internal "sites" alerts, the most of any source |
| Stalled-WhatsApp watcher | Every 30 minutes, raises an alert when an internal alert has waited more than 2 hours |
| Learning notebook | Measures, 14 days later, what Velox did |
| Trial clock | Makes the 7 trial days count only while the store is active and has an active listing |
| Abandoned charges | Cancels a Pix 5 days after its due date and a card that has not paid for 7 days, every 6 hours |
| Asaas reconciliation | Re-reads the subscriptions in Asaas every 3 hours |
| Piece producer | Narrates with its own voice generator and renders video with HyperFrames, with no language model. Runs on the desktop worker |

---

## Not running yet

These are planned. They are not features today.

- **Radar** (catalog competition) and **Shipper** (batch labels and invoices). Both appear to the seller as "in training".
- **Retention and Expansion** on the growth floor.
- 19 other roles planned in the registry: 8 for Instagram (among them a clips editor, an art director and a community manager), 2 for ads, and roles for intelligence, rules monitoring, client success, finance, product, engineering and knowledge.

In total, 22 registered roles do not run today. They are not counted in the 26 + 30.

Not to be confused: the Radar above is not the Copier's "what is selling" screen (read-only) and not the Instagram competitor radar.
