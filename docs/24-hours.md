# The 24-hour routine

[Back to the README](../README.md)

What runs at Velox, how often, and who does it. Read from the code and the n8n database on Nov 14, 2026.

**How to read this table**

- App routines never overlap. If the previous run is still going, the next one is skipped. Intervals count from the last restart of the process.
- Fixed times are Brasília time (UTC-3). Two exceptions use the UTC date (which turns at 21:00 in Brasília): the on-call robot's daily alert key and the daily Meta account check.
- "Internal alert" means a row in the internal alerts table. n8n delivers it to the founder's WhatsApp between 7:00 and 22:30.
- Names in the "Who" column are explained in [agents.md](agents.md).

## The schedule

| What | How often / when | Who (where it runs) |
|---|---|---|
| **MERCADO LIVRE STORES** | | |
| Store read queue | Every 3 s, one read at a time until the queue is empty; 24 h. If Mercado Livre answers 429 or 5xx, or the network fails or times out, the queue stops for 5 min | Store read (app) |
| First-read notice | When a store's first read ends; once per store | App |
| Re-read scheduler | Checks every 5 min. A store goes back to the queue when its last good read started more than 5 h 30 min ago (about 4 reads per store per day) | App |
| Team shift | At the end of every read that completes. The Strategist runs once a day per store | Sentinel, Listing Expert, Finance, Shipping Watcher, Lister, Copier, Stock Keeper, Recovery agent, Strategist (app) |
| Mercado Livre notifications in | When a notification arrives (`items`, `items_prices`, `orders_v2`, `questions`); up to 120 per minute per account and topic | App |
| Notification processing | Every 15 s, batches of 50. On error, retried after 2, 4, 8 and 16 min; dropped on the 5th attempt | Sentinel watches; group stock is checked (app) |
| Live sale | When an order notification arrives. The new-sale notice is grouped every 30 min per store | Stock Keeper (central stock) and Finance (app) |
| Stock sales (backup sweep) | Every 3 min; up to 20 pages of 50 per run, at most 15 days back | Stock Keeper (app) |
| Buyer question | When a question notification arrives | Customer Service (app) |
| Question sweep | Every 3 min. A question without an answer for more than 1 hour (last 72 hours, up to 20 at a time) becomes a notice in the feed | Customer Service (app) |
| Automatic answers | Every 15 s, up to 20 per run; 24 h. A "confident" answer waits 3 min, then goes out | Customer Service (app) |
| Shipping sweep | Every 30 min. First up to 10 Multiply copies from the last 48 hours, then free-shipping listings read more than 2 hours ago, up to 40 per store | Shipping Watcher (app) |
| Stock increases and outside drops | Every 60 s | Stock Keeper (app) |
| Stock cancellations | Every 30 min, up to 100 orders | Stock Keeper (app) |
| Daily report | Every 10 min; closes up to 7 days back. Yesterday's report from 1:00 if the orders were read after midnight, otherwise from 8:00 | Reporter (app) |
| Mercado Livre tokens | Every 30 min. Renews an account that expires in less than 70 min, or that was not renewed in 3 days | App |
| Spreadsheet of the day | Every 30 min; up to the day's quota (default 60) and 20 per run | Lister (app) |
| Listing queues | AI drafts every 4 s, spreadsheet every 5 s, products and photos every 15 s, AI specs every 4 s, specs to check every 60 s. Only when someone asked | Recovery agent, Lister, Copier, Listing Expert (app) |
| Google Drive folder | Every 1 h | Lister (app) |
| Learning notebook | Every 6 h; measures, 14 days later, what Velox did | Learning notebook (app) |
| **NEWSROOM (VELOX NOTÍCIAS)** | | |
| Newsroom missions | Every 60 s, up to 16 steps per mission per run. The first at the newsroom time (default 7:30), the others every 2 hours, only those that start by 19:00. Missions per day: 0 to 8 (code default 1). With 7:30, at most 6: from 7:30 to 17:30. In use: 6 a day | System, Source Curator, Marketplace Researcher, SEO Writer, Editorial Supervisor (app) |
| Publishing | Inside the 60 s run; from 8:00 to 20:00, at most 6 a day, 2 hours apart. Publishes on its own only when the publisher's autonomy is set to the supervisor. Set to the supervisor since Oct 29, 2026 | Publisher (app) |
| Reading the sources | Inside a mission step; each source at most every 170 min | Source Curator (app) |
| Measuring each article | At 24 hours and at 7 days | Content Analyst (app) |
| Article art | Every 60 s, up to 4 per run; on error, tries again in 30 min | Art studio (app) |
| Search engines (IndexNow) | Every 60 s; if refused or down, waits 30 min | Search engine notice (app) |
| E-mail alerts | Every 60 s; an article older than 6 hours does not become an alert; 3 alerts per run | News alerts (app) |
| **INSTAGRAM** | | |
| Production | Every 10 min. Keeps 3 slots filled; does not start less than 60 min before a slot; 4 hours between posts; up to 9 productions and a daily AI cap (default US$ 4) per day. Code grid: Monday to Friday at 8:00, 12:00 and 18:30 (the Reel only when Reels are on); the grid in use is in the database | Scriptwriter, code checks, Social Supervisor and council, art (app) |
| Publish | Every 10 min from 7:00 to 22:50; up to 3 hours late, 3 attempts | Instagram Publish (n8n) |
| Metrics / competitor radar / weekly analysis | Every day at 5:30 / every day at 6:10 / Monday at 7:00 | Instagram Metrics, Instagram Radar, Weekly Analysis (n8n) |
| Hook Hunter | Checks every 30 min; once a day from 7:00 | Hook Hunter (app) |
| Reels Director | Checks every 30 min; once a day from 22:00 | Reels Director (app) |
| Piece producer (voice and render) | Asks the server every 60 s; only while the desktop worker is on | Piece producer (desktop worker) |
| **WHATSAPP** | | |
| João (CRM) | Every incoming message (webhook from the WhatsApp gateway); at most 40 messages a day per number | João (n8n and app) |
| Trial journey messages | Every 15 min from 9:00 to 19:45, every day (the app only answers from 9:00 to 20:00). No store yet: 1 hour, 1 day, 4 days. Welcome: 10 min to 2 days after connecting. Stopped logging in: 2 days. Summary: day 5, only with a finding. End of trial: 20 hours to 14 days later, when that message is switched on | Trial Guide (n8n and app) |
| Subscription reminders | 10:00, 15:00 and 19:00, every day; up to 50 per call | Subscription Reminders (n8n and app) |
| Internal alerts to the founder | Every 30 min from 7:00 to 22:30; up to 20 pending alerts per call | Internal alerts workflow (n8n and app) |
| WhatsApp gateway on the server | Always on; systemd restarts it in 10 s | Server |
| **BILLING** | | |
| Asaas payment event | When Asaas sends an event; each event is processed once | App |
| Pix receipt on WhatsApp | When a receipt arrives (through the CRM) | Pix Receipt Reader (n8n and app) |
| Asaas reconciliation | Every 3 hours | Asaas reconciliation (app) |
| Overdue Pix and failing card | Every 6 hours, up to 50 per run | Abandoned charges (app) |
| Trial clock | Every 10 min | Trial clock (app) |
| End of trial | From the journey and alert rows: João's message 20 hours to 14 days later, and an internal "trial ended" alert | n8n and app |
| **ALERTS** | | |
| Stalled WhatsApp | Every 30 min. An internal alert waiting more than 2 hours becomes a phone alert; at most 1 every 6 hours | Stalled-WhatsApp watcher (app) |
| Site alerts | Every 15 min; at most 3 alerts a day. R1 site down: from 12:00. R2 silence: 9:00 to 21:00. R3 trial button: from 21:00. R4 and R5: any time. R6: stalled e-mail any time, articles below target from 20:00 | Site watcher (app) |
| **ON-CALL** | | |
| On-call robot | Every 10 min; 8 watchers, SQL only. A new serious alert goes to WhatsApp, once per alert key per day (UTC) | On-call (app) |
| **GROWTH FLOOR / META ADS** | | |
| Growth floor loop | Every 10 min, one process at a time. Funnel meter: once a day from 6:20. With the Executor's token: account checked once a day (UTC) and performance every 30 min; spend at the cap pauses the campaigns. Treasury and Executor on every run | Funnel Meter, Treasury, Campaign Executor (app) |
| Growth AI schedule | Maestro Monday to Saturday 9:05; Data Monday and Thursday 8:20; Intelligence Tuesday and Friday 10:10; Positioning day 1 at 10:30; Paid Media Manager Monday to Saturday 11:10; Activation Wednesday 15:10; Learning Curator Friday 16:10; Copywriter, Scriptwriter and QA only from the queue. 30 rounds a week by default (0 to 80). AI through the desktop worker by default | The 10 growth agents (app and desktop worker) |
| Meta events (Conversions API) | Every 5 min | App |
| Ready pieces | Every 5 min, up to 3 | App |
| **SITE MEASUREMENT** | | |
| Site counter | Every 60 s; hourly totals every 5 min; closes yesterday from 0:15 and again from 3:00; every 6 hours deletes raw rows older than 35 days | App |
| **BACKUPS, SERVER AND THE REST** | | |
| Backup (app, n8n and WhatsApp gateway) | Every day at 3:00; 3 encrypted files; 7 days on the server and 30 off-site | Server (cron) |
| App and n8n | Always on; systemd restarts the app in 3 s and n8n in 5 s | Server |
| App cleanup | Every 6 hours | App |
| Admin panel cleanup | At start and every 6 hours | App |
| In-memory counter cleanup | Every 10 min | App |
| Recovery at start | On every restart | App |
| Certificates | Twice a day (certbot timer at 00:00 and 12:00, with a random delay of up to 12 h) | Server |
| Desktop Claude worker | Asks the server's AI queue every 20 s | Desktop worker |
| HQ newsroom team / HQ summary for the admin | Asks the server every 5 s / on every change (at most once a second) with a heartbeat every 10 s | Desktop worker |
| Contract generator; tunnel address | Switched off | n8n |

## A day at Velox

A weekday, built only from the table above.

**All day and all night.** The store read queue moves every 3 seconds. Every 15 seconds, Mercado Livre notifications are processed and confident answers to buyers go out after their 3-minute wait. Every minute, the newsroom engine, the site counter and the stock checks run. Every 3 minutes, buyer questions and stock sales are swept. Every 5 minutes, stores that need a fresh read go back in the queue, and Meta events are sent. Every 10 minutes, the daily reports, Instagram production, the growth floor, the on-call robot and the trial clock run. Every 30 minutes, tokens are renewed and shipping costs are checked. Every 3 hours, subscriptions are re-read from Asaas. Every 6 hours, overdue charges are cleaned up and the learning notebook measures what Velox did 14 days earlier.

And whenever something happens: a sale, a buyer question, a price change on Mercado Livre, a WhatsApp message, a payment event in Asaas.

**0:15.** The site counter closes the day before.

**From 1:00.** The Reporter closes yesterday's daily report for each store, if the orders were read after midnight. If not, it waits until 8:00.

**3:00.** The server makes the daily encrypted backup. The site counter closes yesterday again.

**5:30.** n8n reads the Instagram metrics. **6:10.** It reads the competitors' public posts. **From 6:20.** The funnel meter counts the day.

**7:00.** Internal alerts start reaching the founder's WhatsApp (every 30 minutes, until 22:30), including the ones that waited overnight. The Instagram publisher starts its 10-minute runs (until 22:50). The Hook Hunter runs. On Mondays, n8n writes the weekly Instagram analysis.

**7:30.** The first newsroom mission starts (default time).

**8:00.** The newsroom's publishing window opens (until 20:00, at most 6 articles, 2 hours apart). The first Instagram slot of the default grid. On Mondays and Thursdays at 8:20, the data analyst of the growth floor runs.

**9:00.** João's trial journey messages start (every 15 minutes, until 19:45). The site watcher starts checking for silence (until 21:00). **9:05.** The Maestro sets the growth floor's priorities.

**10:00.** Subscription reminders. On Tuesdays and Fridays at 10:10, market intelligence runs. On the 1st of the month at 10:30, positioning. **11:10.** The Paid Media Manager reviews the campaign.

**12:00.** The carousel slot on Instagram. The site watcher starts checking for a site that is down.

**15:00.** Subscription reminders. On Wednesdays at 15:10, activation and conversion. On Fridays at 16:10, the learning curator.

**18:30.** The Reel slot on Instagram (only when Reels are on). **19:00.** Subscription reminders. No new newsroom mission starts after this time.

**20:00.** The newsroom's publishing window closes. The site watcher checks whether the day's articles met the target.

**21:00.** The site watcher checks the trial button and stops checking for silence. **From 22:00.** The Reels Director writes the goal for the next Reel.

**22:30.** The last delivery of internal alerts. **22:50.** The last Instagram publishing run. From 22:30 to 7:00, internal alerts wait in the queue.

**On weekends,** the default Instagram grid has no posts. The Maestro and the Paid Media Manager still run on Saturdays. The trial journey, reminders, internal alerts and the newsroom run every day.
