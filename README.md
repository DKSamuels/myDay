# myDay — Personal CRM on Claude Desktop

*Track your relationships. Stay on top of commitments. No expensive CRM subscriptions required.*

---

A lightweight personal CRM that runs entirely on your machine, powered by Claude Desktop and your existing connections — Gmail or Outlook, Google or Outlook Calendar, and optionally a meeting notes tool like Granola or Otter.

It's built for situations where a spreadsheet isn't enough but a full CRM is overkill. If you're managing a job search, running a fundraising effort, building an advisory network, or doing business development — and you have dozens of contacts you need to stay current with — this gives you the structure to stay on top of it without adding another tool or another monthly bill.

Your data lives in a folder on your computer as simple JSON files. Claude reads and writes those files directly through your local Cowork workspace, processes your email and calendar metadata, and surfaces what you need to act on. You will need a Claude Pro account ($20/month) to use this.

---

## What's in this repo

| File | What it is |
|---|---|
| `README.md` | This overview |
| [`SETUP.md`](SETUP.md) | Step-by-step setup guide — start here after reading this |
| [`skill/SKILL.md`](skill/SKILL.md) | The installable myDay skill file |

---

## Projects

The CRM is organized around projects — discrete goals where you're managing a set of contacts toward an outcome. You can run multiple projects at the same time, completely independently.

A project can be anything where you have a lot of people, phone numbers, and follow-ups to track:

- A job search — recruiters, hiring managers, warm intros, references
- A fundraising round — investors, warm intros, term sheet conversations
- A school committee or auction — volunteers, vendors, parents to coordinate
- Business development — prospects, partners, referral sources
- An advisory network — mentors, advisors, connectors you want to keep warm

Each contact belongs to a project. The digest, relationship health tracking, and commitment extraction all work at the project level. You define up to five projects during setup and can update them at any time.

---

## What it tracks

**Contacts** — one record per person, organized by project. Who they are, how you know them, their company and role, and the full history of your interactions.

**Interactions** — every meaningful touchpoint, with a short summary of what was discussed. Email threads and calendar events are pulled automatically. Phone calls, texts, coffee meetings, LinkedIn conversations, and anything else that happens outside your inbox gets logged with a quick chat command — a few sentences, and the record is updated.

**Commitments** — things you said you'd do, extracted from your meeting notes and emails. Sent a follow-up? Made an intro? Promised to share something? The system tracks these and flags them when they're overdue.

**Relationship health** — a running sense of who you've been in contact with recently and who's going quiet. The digest flags the contacts you haven't talked to in a while so you can decide whether to reach out.

---

## How it works day to day

Once you set it up, the system runs a digest on the schedule you choose — daily, a few times a week, or weekly. It takes a few minutes to review each time.

Each digest surfaces a short set of decision cards:
- A commitment that's overdue or coming due
- A relationship that's cooling
- A new contact from your email that needs to be assigned to a project
- A contact whose project assignment needs confirmation

You respond to each card in the chat, Claude updates the files, and you move on.

For everything else, you interact through simple chat commands:

- **Look up a contact** before a call to get full context in 60 seconds
- **Get a pre-meeting brief** with talking points, interaction history, and open commitments
- **Log a note** after any interaction that isn't captured automatically — a phone call, a text, a coffee, a LinkedIn DM thread. Describe what happened; the system extracts any commitments and updates the record
- **Mark a commitment done** as you work through your list
- **Review a project** to see which contacts are current, cooling, or cold

---

## LinkedIn

LinkedIn is where a lot of relationship activity happens — especially for job searches and business development. It's also where you find context on people before you meet them.

The CRM doesn't pull LinkedIn automatically, but it works alongside it in two practical ways.

First, when you're researching a contact or preparing for a call, you can ask Claude to look them up on LinkedIn. It pulls their current role, background, mutual connections, and anything relevant from their recent activity — and that context gets added to their contact record for next time. This works through the [Claude in Chrome browser extension](https://claude.ai/download).

Second, if you have a meaningful exchange on LinkedIn — a DM thread that goes somewhere, a connection who responds to an outreach — log it the same way you'd log a phone call. Tell Claude what happened and it updates the interaction history and extracts any commitments.

The combination keeps LinkedIn from being a black hole where context goes to die.

---

## Morning prep

An optional feature automatically checks your calendar each morning, researches everyone you're meeting with that day, and drops a brief per person into a `briefs/` folder in your workspace — ready before you start your day.

Each brief draws from your CRM history, your meeting notes tool (if connected), LinkedIn, and optionally the web. You choose the depth: quick, standard, or deep research. The files are named `YYYY-MM-DD - Name - Company.md` (or `.docx` or `.txt`, your choice) and sorted cleanly by date.

---

## What it's not

It's not a sales CRM. It won't manage a pipeline with stages, deal values, and probability forecasting. It doesn't send emails on your behalf or automate outreach sequences.

It's also not a team tool. Everything here is personal — your contacts, your interactions, your commitments. If you need shared access or collaborative record-keeping, you need something else.

---

## What you need

**Required:**
- Claude Desktop with Cowork mode — [download here](https://claude.com/download)
- Claude Pro account ($20/month) — [claude.ai](https://claude.ai)
- Gmail or Outlook connected to Cowork
- Google Calendar or Outlook Calendar connected to Cowork
- A folder on your computer to use as your workspace

**Optional but recommended:**
- [Claude in Chrome browser extension](https://claude.ai/download) — enables LinkedIn research
- A meeting notes tool (Granola, Otter, Fireflies, Fathom, or similar) connected to Cowork

No API keys. No database. No external infrastructure. Claude does the work; your local folder holds the data.

---

## Getting started

Read [`SETUP.md`](SETUP.md) and follow the steps in order. Setup takes about 45 minutes the first time. After that, open the digest on your chosen schedule, respond to the cards, and the system runs itself.

---

*Built on Claude Desktop · Your data stays on your machine*
*Not affiliated with Anthropic. Requires a Claude Pro subscription.*
