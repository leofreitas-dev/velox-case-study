# Using Velox for another business

[Back to the README](../README.md)

Velox was built for Mercado Livre sellers. But the model behind it, **a 24-hour AI team with human approval**, can be adapted to other businesses. This page says honestly which parts are generic, which parts are tied to Mercado Livre, and how much work an adaptation would take.

The estimates below are **estimates, not promises**.

## Two layers

1. **The business machine around the product.** The WhatsApp agent, the CRM, the newsroom, the Instagram engine, billing, site measurement, the admin panel, the HQ, AI cost control, the on-call robot and the server. The mechanism is almost all generic. The texts are written for Velox.
2. **The product for Mercado Livre sellers.** Tied to Mercado Livre from end to end.

## Facts about the code (as of Nov 14, 2026)

- The main app has 264 TypeScript files, about 75,000 lines. 34 files import the Mercado Livre client, 97 mention the table of connected Mercado Livre stores, 45 mention Mercado Livre item IDs, and **72 files mention nothing from Mercado Livre**.
- Today each business needs its own installation. There is no company layer: one configuration for the whole installation.
- The app does not start without the Mercado Livre settings, and the scheduler keeps Mercado Livre routines and generic routines in the same file. **Separating that core is the first job in any adaptation.**

## Generic parts

| Part | What is already generic | What is tied to Velox or Mercado Livre |
|---|---|---|
| **WhatsApp agent (João) and its knowledge base** | A protected CRM entry point; a cap of 40 messages per number per day; the stalled-WhatsApp watcher; Pix receipt reading where code, not AI, decides; the gateway installation; a knowledge base kept as loose documents in Google Drive (a setting); the website assistant with CORS, a per-IP limit and a spending cap | The SaaS journey with a store, and texts about "your Mercado Livre store"; the website assistant's prompt, written for Velox and Mercado Livre; a knowledge base full of Mercado Livre content |
| **CRM in n8n** | Tables for leads, blocked contacts, the inbox and settings; a contract generator (switched off) | `velox_*` names, fixed IDs in the permission SQL, nodes that read the client from the app |
| **Newsroom** | A source curator that respects robots.txt; sources stored in the database; missions with an engine, a brake and a cap | Marketplace sources seeded in the database, sections, topic rules, pages and a Black Friday guide for Mercado Livre |
| **Instagram engine** | The routine, the art, the learning and the director; n8n workflows for publishing, metrics and radar; brakes in the database | Seeded facts, Velox's checks, content pillars, the council, the seeded @ handles |
| **Billing and subscription (Asaas)** | An Asaas client with no Mercado Livre code (sandbox, recurring Pix, card, webhook); abandoned Pix; periodic reconciliation | Price by number of stores; a trial clock that needs a store and a listing; only Pix and card |
| **Admin panel** | Its own server; accounts, invitations, authenticator, expenses, dollar rate, finance and Instagram, with no Mercado Livre code | Client and store numbers come from the table of Mercado Livre stores; the front-end contract and the Overview, System and Clients modules |
| **Velox AI HQ** | Reads Claude Code sessions; floors and rooms come from configuration; only 6 of its 153 files mention Mercado Livre | The brand, "Velox Tower" and the 11 store employees. It only makes sense for a business that also works with Claude Code |
| **Site measurement and pixel** | No Mercado Livre code; collection without IP, with a daily hash; the Meta Ads executor as a bonus | Fixed domains, SaaS funnel events, a "store connected" event |
| **AI cost log and caps** | Every call becomes a row with tokens and cost; a model route per purpose; caps per sector and per role | Part of the purposes are Mercado Livre tasks; some routines run through a desktop Claude worker (for another business, they would run through the API) |
| **On-call robot and alerts** | The on-call mechanism; watchers for the AI queue and AI errors; the internal alert queue | The other watchers are about Mercado Livre; the phone alert's backup path does not work today |
| **Backups and installation** | Server scripts, encrypted backup, deploy, n8n and WhatsApp gateway installation, with no Mercado Livre code | The name "velox" in paths, nginx per domain, keys and the backup destination |

## Parts that only work with Mercado Livre

OAuth and the API client; the store read; the listing score; technical specs; titles; copies (Multiply and the Copier); bulk listing from a spreadsheet (the spreadsheet, zip, photo and Drive readers can be reused); central stock (the stock ledger can be reused); finance with Mercado Livre fees (the formula itself is pure code); DIFAL (pure rules, with invoices read from Mercado Livre); the Shipping Watcher; buyer questions (the per-client knowledge base can be reused).

Also: the whole store team module (about 4,900 lines) and about 12,900 lines of screens and routes.

## Estimated effort

In business days. **An estimate, not a promise.**

| Scenario | Estimate |
|---|---|
| (a) Your own online store, base | **46 to 72 business days**, about 9 to 14 weeks |
| (a) with DIFAL, buyer questions and the shipping watcher | **52 to 84** |
| (a) if it also sells on Mercado Livre and wants a single stock | **8 to 12 more** |
| (a) only the business machine, without running the store | **25 to 40** |
| (b) A service provider (a clinic, an office or a school) | **29 to 47 business days**, about 6 to 9 weeks. The biggest unknown is scheduling, which does not exist today |

Subtotals: generic parts 25 to 40 days for (a) and 29 to 47 for (b); required Mercado Livre parts 21 to 32 days for (a) and 0 for (b).

**The estimate assumes:**

- one person who already knows the code, working full days with Claude Code (the 107 test files help);
- one platform only (Shopify or Nuvemshop; WooCommerce varies);
- one company per installation;
- the owner delivers and approves content on time;
- the n8n workflows are adapted from the project notes. This is the most uncertain part.

**Not included in the estimate:**

- external accounts and approvals (the platform, Meta, Asaas, WhatsApp);
- legal review (Brazil's LGPD and professional councils);
- tuning with a real client;
- moving WhatsApp from the WhatsApp Web gateway to the official WhatsApp Cloud API.

## Talk about it

If you want a 24-hour AI team with human approval for your own business, book a call: https://cal.com/leofreitas-dev/intro or write to leonardofreitasdev06@gmail.com.
