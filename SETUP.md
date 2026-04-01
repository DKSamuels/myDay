# Personal CRM: Setup Guide
*myDay | Personal use | No expensive CRM subscriptions required*
*Version 1.0, March 2026*

---

## Before you do anything else

**1. Get Claude Desktop running.**

Go to [claude.com/download](https://claude.com/download). Download and install the app. You need a Claude Pro account ($20/month) to use Cowork. Sign up at [claude.ai](https://claude.ai) if you don't have one. If you're new to Claude and want to understand what it can actually do before diving in, [start here](https://open.substack.com/pub/ruben/p/claude?utm_campaign=post-expanded-share&utm_medium=web).

**2. Create your workspace folder and install the skill file.**

This is the step most guides skip. The skill file is what teaches Claude the myDay commands. Without it, nothing below will work.

1. Create a new folder on your computer. Put it somewhere you'll find it again. "myDay" on your Desktop is fine.
2. Inside that folder, create this folder path: `.claude/skills/myday/`
3. Download `SKILL.md` from this repo (it's in the `skill/` folder). Save it to `.claude/skills/myday/SKILL.md` inside your new folder.
4. Open Claude Desktop. Click the **Cowork** tab at the top. Click **Select Folder** and choose the folder you just created.

Your workspace is now active. The skill loads automatically when you open a Cowork session in this folder.

**3. Connect your integrations.**

myDay needs access to your email and calendar. These are the connections that make the digest, the email sweep, and the morning prep work. Without them, the system has nothing to read.

To connect:
1. In Claude Desktop, click the **Cowork** tab
2. Click **Integrations** (or the plug icon) in the sidebar
3. Find Gmail or Outlook and click **Connect** — you'll be prompted to sign in and authorize access
4. Do the same for Google Calendar or Outlook Calendar
5. Once both show a green connected status, you're set

**Required:**
- Gmail or Outlook
- Google Calendar or Outlook Calendar

**Optional:** A meeting notes tool like Granola, Otter, Fireflies, or Fathom. If you use one, connect it now. If not, skip it — you can add it later. myDay works without it; you'll just log meetings manually.

**Verify before moving on.** Open a Cowork session and paste this:

```
Check my connected integrations. Confirm you can access:
1. My email (Gmail or Outlook) — list the 3 most recent email subjects
2. My calendar — list today's events or the next 3 upcoming events

If either check fails, tell me which one and what error you see.
```

If both return results, you're ready. If one fails, fix the connection in the integrations panel before continuing. The setup steps won't work without it.

---

## Which model to use, and when

Claude has three models. This system uses all three, but for different jobs. Using the right model for the right task keeps setup fast, keeps the daily digest cheap, and makes sure the parts that need real judgment get it.

| Task | Model | Why |
|---|---|---|
| Step 1: Initial setup | **Opus 4.6 + Extended Thinking** | You're defining how the system understands your relationships. One-time work. Get it right. |
| Step 3: Initial email sweep (review queue) | **Sonnet** | Large-scale pattern matching plus judgment calls on which contacts matter. |
| Steps 2, 4, 5: Sweeps and enrichment | **Haiku** | Structured retrieval. Fast and accurate. No deep reasoning needed. |
| Step 6: Digest scheduling setup | **Sonnet** | One-time configuration. Worth the extra precision. |
| Step 7: Morning prep setup and all runs | **Sonnet** | Multi-source research and synthesis. Needs real judgment. |
| All scheduled digest runs | **Haiku** | Runs automatically. Structured output. Haiku handles this well and keeps cost low. |
| Day-to-day: lookup, brief, note, done | **Haiku** | Fast retrieval from your local files. Haiku is the right tool. |
| Complex digest decisions | **Sonnet** | If a card requires real judgment, like an ambiguous contact or tricky classification, switch to Sonnet for that decision. |

**How to switch models:** Click the model selector at the top of your Cowork session before pasting a prompt.

---

## Part 1: One-Time Setup
*~45 minutes total. Run all steps in order.*

---

### Step 1: Initialize myDay
**Model: Opus 4.6 + Extended Thinking**

Open a new Cowork session in your workspace folder. Paste this prompt:

---
```
Set up my Personal CRM in Cowork.

Create the following file structure in my workspace folder:
- crm/config.json         (project definitions and classifier settings)
- crm/contacts/           (one JSON file per contact, named by email address)
- crm/index.json          (master contact index for fast search)
- crm/interactions.json   (running interaction log)
- crm/commitments.json    (open and resolved commitments)
- crm/preferences.json    (classifier learning, starts empty)

Before creating any files, help me define my active projects.

Start by presenting these four starter options as a numbered list
and ask me which ones I want to activate:

1. My Network — professional relationships I want to maintain and keep warm
2. Job Search — recruiters, hiring managers, warm intros, references
3. Business Development — prospects, partners, referral sources
4. Advisors & Mentors — people I learn from or go to for counsel

Then ask: "Would you like to rename any of these, or add a project
of your own? You can run up to 5 projects total."

For any project I activate or add, collect:
- Name (confirmed or custom)
- One-sentence description of what I'm working toward
- Any specific company domains or keywords that signal relevance
  (optional, I can add these later)

After I confirm my project list, confirm the config and create all
files. Then confirm setup is complete and tell me the next step.
```
---

**What to expect:** Claude will present the four starter projects and ask which ones apply to you. Select the ones that fit, skip the ones that don't, and add your own if you have something specific in mind. One sentence per project is enough. The classifier learns from your decisions over time.

---

### Step 2: Seed your contacts
**Model: Haiku**

Run this immediately after Step 1, before the email sweep. There are two ways. Use either one, or both.

**Option A: Email scan (recommended if you have 90 days of email history)**
Pulls your top 30 most-emailed external contacts and asks you to sort them: suppress, track, or skip. Fast way to get the right people in and the noise out before the full sweep runs.

**Option B: Manual seed (use this if you'd rather start with a specific list or project)**
You name the people or describe the project, and Claude builds the initial contact records directly. Good for situations where email doesn't reflect who actually matters: a new job search, a fresh fundraise, a project that's mostly been calls and meetings so far.

You can also do both: run the email scan first, then add a manual seed for anyone it missed.

---

**Option A prompt:**
```
Run the suppression sweep for my Personal CRM.

I'd also like to seed the CRM manually in addition to the email scan.
Ask me after the sweep if I want to add anyone directly.

1. Query my Gmail for all emails sent or received in the past 90 days.
   Use metadata only: from, to, cc, subject, date. Do not read email bodies.

2. Identify the top 30 external contacts by email frequency
   (most emails exchanged with me in the past 90 days).

3. For each of those 30 contacts, show me a card with:
   - Name and email address
   - Approximate email count in the past 90 days
   - Most recent email subject
   - Your suggested action: SUPPRESS (working contact) or REVIEW (may be relevant)

4. Present these as a numbered list. For each one I'll tell you:
   SUPPRESS, TRACK (and which project), or SKIP for now.

5. After I respond, update crm/contacts/ and crm/preferences.json accordingly.

6. Then ask: "Would you like to add anyone manually before the full email sweep?"
```
---

**Option B prompt:**
```
Seed my Personal CRM manually.

Ask me to describe who I want to start tracking. I may give you:
- A list of specific names or email addresses
- A project I'm working on ("I'm doing a job search and want to track
  these recruiters and hiring managers...")
- A mix of both

For each person I name:
1. Collect: name, email (if I have it), company, role, project, and
   how we know each other or how they came up
2. Check crm/index.json for duplicates before creating
3. Create the contact record in crm/contacts/ and update crm/index.json

After I've finished adding people, show me a summary of who was added
and confirm we're ready to move to the email sweep.
```
---

**Decision guide for Option A:**
- **SUPPRESS** if: you email this person more than once a week, they're on your team, they're a service provider in active daily contact, or they're a vendor/admin contact
- **TRACK** if: you'd want context on this person before a call but don't talk to them constantly
- **When in doubt:** suppress. You can reverse it at any time.

---

### Step 3: Initial email sweep
**Model: Haiku** (switch to **Sonnet** when reviewing the classification queue)

Run after the suppression sweep. Builds your contact database from 90 days of email history.

---
```
Run the initial email sweep for my Personal CRM.

1. Query my Gmail for all emails sent or received in the past 90 days.
   Use metadata only: from, to, cc, subject, date, thread ID.
   Exclude:
   - Newsletters (unsubscribe headers, bulk send indicators, no-reply addresses)
   - Automated senders (receipts, order confirmations, shipping notifications)
   - Spam and promotions (Gmail labels: Promotions, Spam, Updates)
   - Anyone already marked suppressed in crm/contacts/
   - Sensitive categories: medical providers, financial institutions, legal
     correspondence. Exclude these silently, do not surface them in review.

2. For each unique external contact not already in the CRM:
   a. Calculate total email exchanges and most recent email date
   b. Classify against my active projects in crm/config.json
   c. If confidence >= 0.80: auto-create a contact record and assign to project
   d. If confidence < 0.80: add to the review queue

3. Create contact records in crm/contacts/ for all auto-classified contacts.
   Update crm/index.json.

4. Present the review queue: contacts needing manual project assignment.
   For each, show: name, email, company (from signature if available),
   most recent email subject, and your best guess at the right project.
   I'll confirm, reassign, or skip each one.

5. After I respond, finalize all contact records and show me a summary:
   total contacts created, breakdown by project, and count skipped.
```
---

> **Note:** The initial sweep takes a few minutes. It's processing 90 days of email history. The review queue that follows is the most important part. Your responses train the classifier for all future sweeps. Switch to Sonnet when working through the queue if you want stronger judgment on ambiguous contacts.

---

### Step 4: Signature enrichment
**Model: Haiku**

Run after the email sweep. Reads the signature block of the last email per contact to extract phone numbers, LinkedIn URLs, titles, and company details. No full email bodies.

---
```
Run a signature enrichment pass on my Personal CRM contacts.

For each contact in crm/contacts/ where enrichment_status is null or missing:

1. Find the most recent email thread between me and this contact
2. Read only the last message in that thread, specifically to extract
   any email signature block at the bottom of the message
3. From the signature, extract if present:
   - Phone number(s), labeled as mobile or office if identifiable
   - LinkedIn URL
   - Job title (if different from what we already have)
   - Company name (if explicitly stated)
   - Website
4. Update the contact record with any new fields found.
   Mark enrichment_status: "signature_parsed"
5. For contacts where no signature was found or no new data extracted,
   mark enrichment_status: "no_signature"

Process contacts in this priority order:
1. Contacts with the highest email frequency (most active relationships first)
2. Contacts where title or company is currently blank
3. Remaining contacts

After completing, show me a summary:
- Total contacts processed
- Contacts enriched with new data
- Most common fields found (e.g., "phone: 34 contacts, LinkedIn: 28 contacts")
- Contacts where no signature data was available
```
---

---

### Step 5: Meeting notes initial sync (optional)
**Model: Haiku**

**Skip this step if you don't have a meeting notes tool connected.**

If you use Granola, Otter, Fireflies, Fathom, or a similar tool and it's connected to Cowork, run this after signature enrichment. Backfills meeting context and extracts your first batch of commitments from the past 90 days.

Tell Claude which tool you're using, then paste this prompt, replacing `[TOOL NAME]` with your tool:

---
```
Run the meeting notes initial sync for my Personal CRM.
My meeting notes tool is: [TOOL NAME]

1. Query [TOOL NAME] for all meeting notes from the past 90 days.

2. For each meeting note:
   a. Identify all participants by email address
   b. Skip participants who are suppressed in crm/contacts/
   c. For participants already in the CRM: create an interaction record
      in crm/interactions.json, source: [TOOL NAME], type: meeting,
      with a 2-3 sentence summary of the meeting
   d. For new participants not yet in the CRM: check if they should be
      added based on my active projects. If yes, create a contact record
      and interaction. If unclear, add to review queue.

3. Extract commitments from each meeting note.
   Look for:
   - Promises I made: "I'll send...", "I'll connect you with...", "I can get you..."
   - Agreed follow-ups: "We'll reconnect...", "Let's schedule..."
   - Intro commitments: "I'll make the introduction to..."
   - Action items assigned to me
   Create commitment records in crm/commitments.json for each one found.

4. Show me a summary:
   - Meetings processed
   - New interactions created
   - New contacts added
   - Commitments extracted (list all so I can review)
   - Review queue items (if any)
```
---

> **Review the extracted commitments carefully.** Some will be accurate. Some will be false positives, like casual mentions that weren't real commitments. Dismiss anything that's not genuine. This trains the extractor.

> **Don't have a meeting notes tool?** You can add one later and run this sync at any time. myDay works without it. Calendar events become interaction stubs, and you can log any call or meeting manually with the NOTE command.

---

### Step 6: Set up the digest
**Model: Sonnet** (for setup) | **Haiku** (for all runs)

Run this last. Creates the scheduled task that keeps myDay current automatically.

**Choosing your cadence:** Pick the frequency that fits how active your project is right now. You can change it at any time.

| Your situation | Recommended cadence |
|---|---|
| Active job search or fundraise | Daily: high activity means more to process and more things that can go cold fast |
| Steady relationship management | Mon / Wed / Fri: enough frequency to catch things before they slip |
| Slower-moving project, or just starting out | Weekly: lower noise while you're getting familiar with the system |

You can also run the digest on demand at any time. Just say `/myday digest` in Cowork regardless of your schedule.

---
```
Create a scheduled task for my Personal CRM digest.

Task name: "Personal CRM Digest"
Schedule: [CHOOSE ONE]
  - Every day at [TIME] local time
  - Monday, Wednesday, Friday at [TIME] local time
  - Every Monday at [TIME] local time

Model: Use claude-haiku-4-5 for this task.

When the task runs, execute this prompt:

---
Generate my Personal CRM digest. Use claude-haiku-4-5.

1. GMAIL: Query emails sent or received since the last digest run.
   - Identify new external contacts not yet in the CRM
   - Log new interactions with existing tracked contacts
   - Exclude newsletters, automated senders, suppressed contacts, and
     sensitive categories (medical, financial, legal)

2. MEETING NOTES (if connected): Query notes created or updated since
   the last digest run using whatever meeting notes tool is connected.
   - Extract participants, create interaction records, extract commitments
   - Surface any new contacts for project assignment
   - Skip this step if no meeting notes tool is connected

3. CALENDAR: List events since last digest run and coming up today
   with external attendees. Create interaction stubs for tracked contacts.

4. COMMITMENTS: Check crm/commitments.json for open items.
   - Flag any overdue or due within 48 hours

5. RELATIONSHIP HEALTH: Identify tracked contacts with no interaction
   in the past 60 days whose last interaction was a call or meeting
   (not just email). Flag the top 3 as cooling.

6. Present the digest as decision cards, max 10 items, in this order:
   1. Overdue or urgent commitments (highest priority)
   2. Cooling relationships
   3. New contacts needing project assignment
   4. Classification review items

For each card, show the context and response options clearly.
Do not update any files until I've responded to the cards.
---
```
---

**Setup is complete.** The digest runs on your chosen schedule. myDay builds itself from this point forward.

> **Adjusting the schedule later:** Tell Claude "Update my Personal CRM Digest schedule to [new cadence]" and it will modify the scheduled task.

---

### Step 7: Set up the morning prep (optional)
**Model: Sonnet** (for setup and all runs)

**Skip this step if you don't want automated pre-meeting research.**

This creates a second scheduled task that runs each morning, checks your calendar for the day's meetings, and drops a research brief on each external attendee into a `briefs/` folder in your workspace, ready before you walk in.

Each brief pulls from your CRM history, your meeting notes tool (if connected), LinkedIn, and optionally the web. You open a file; it tells you who this person is, what you last talked about, what you owe them, and what to bring up.

**Choosing your depth:**

| Depth | Sources | Best for |
|---|---|---|
| Quick | CRM history + meeting notes | People you already know well |
| Standard | Above + LinkedIn | Default for most external meetings |
| Deep | Above + web research | First meetings, important conversations, fundraising |

You can set a default depth when you create the task, then override it any time by saying "deep prep for today" or "quick prep for [name]."

**Choosing your output format:**

| Format | Best for |
|---|---|
| Markdown (.md) | Viewing in Obsidian, Notion, or any text editor |
| Word (.docx) | Printing, annotating, or sharing with others |
| Plain text (.txt) | Maximum simplicity. Opens anywhere. |

Your choice is saved in `crm/config.json` and used for every brief going forward. You can change it at any time by telling Claude "change my brief format to [format]."

---
```
Create a scheduled task for my Personal CRM morning prep.

Task name: "Personal CRM Morning Prep"
Schedule: Every weekday at [TIME] local time (e.g., 6:30 AM)
Model: Use claude-sonnet-4-6 for this task.
Default depth: [Quick / Standard / Deep]
Brief format: [Markdown / Word / Plain text]

Save the brief format preference to crm/config.json before creating the task.

When the task runs, execute this prompt:

---
Run the morning prep for my Personal CRM. Use claude-sonnet-4-6.
Default depth: [Quick / Standard / Deep]

1. Check Google Calendar for today's meetings.
   Skip any meeting with no external attendees (internal only).

2. For each meeting with external attendees:
   a. Identify all attendees not on my email domain
   b. For each external attendee:
      - Check crm/index.json for existing records
      - Pull last 3 interactions from crm/interactions.json
      - Pull any open commitments from crm/commitments.json
      - Check connected meeting notes tool for shared history
      - Research on LinkedIn (Standard and Deep)
      - Web search for recent news and company context (Deep only)
   c. Compile a brief using the standard format

3. Save each brief to briefs/ in my workspace folder.
   Naming: YYYY-MM-DD - [Full Name] - [Company]
   Extension matches format preference: .md, .docx, or .txt
   One file per person. Do not overwrite an existing brief from today
   without confirming.

4. After all briefs are saved, output a one-line summary:
   "Morning prep complete. [N] briefs saved: [list of names]"
   Do not output the brief content into the chat.
---
```
---

> **Running prep on demand:** You don't need the scheduled task to use this. Any time you want a brief, say `/myday prep` or "prep me for today's meetings" and it runs immediately. The scheduled task just means it's done before you start your day.

> **Briefs folder:** Files land in `briefs/` inside your workspace folder. Open them directly from there. Each file is named by date, name, and company so they sort cleanly.

---

## Part 2: Day-to-Day Use
**Model for all commands: Haiku** (switch to Sonnet for anything requiring real judgment)

After setup, most of your interaction is reviewing the digest. For everything else, use the `/myday` skill. It wraps all the prompts below into simple commands you can run in Cowork without copy-pasting. See `skill/SKILL.md` for installation.

The raw prompts are here as a reference.

---

### Look up a contact

```
Look up [NAME] in my Personal CRM.

Show me:
- Their contact record (company, role, project, how we met)
- Last 5 interactions in chronological order with summaries
- Any open commitments involving them
- Date of last contact
- Relationship health: how often do we typically interact,
  and is the current gap within normal cadence?
```

---

### Pre-meeting brief

```
Give me a pre-meeting brief on [NAME] before my call with them.

Keep it short. I should be able to read this in 60 seconds.

Include:
- One line on who they are and how we know each other
- What we last talked about and when
- Any open commitments (from either of us)
- One or two suggested talking points based on our history
- Anything notable about their company recently (if available)
```

---

### Add a manual note

Use when context won't be captured automatically: a phone call, a text exchange, a coffee, a LinkedIn conversation.

```
Add a manual interaction to my Personal CRM for [NAME].

Date: [DATE]
Type: [call / meeting / coffee / text / LinkedIn / other]
Notes: [WHAT YOU WANT TO REMEMBER]

Also check my notes for any commitments I should extract and track.
Update crm/interactions.json and crm/commitments.json.
```

---

### Mark a commitment done

```
In my Personal CRM, mark this commitment as done:
[DESCRIBE THE COMMITMENT OR PASTE IT FROM THE DIGEST]

If there's a natural follow-up I should create, note it.
Update crm/commitments.json.
```

---

### Review a project's contacts

```
Show me all contacts in my [PROJECT NAME] project from my Personal CRM.

For each contact:
- Name and company
- Last interaction date and type
- Any open commitments
- Status: within normal cadence / cooling / cold

Sort by last interaction date, oldest first.
```

---

### Add a contact manually

```
Add a new contact to my Personal CRM.

Name: [NAME]
Email: [EMAIL]
Company: [COMPANY]
Role: [TITLE]
Project: [PROJECT NAME]
How we met: [CONTEXT]
Notes: [ANYTHING ELSE WORTH CAPTURING]

Create the contact record in crm/contacts/ and update crm/index.json.
```

---

### Suppress a contact

```
Suppress [NAME or EMAIL] in my Personal CRM.
Reason: [working contact / not relevant / other]

Update their record in crm/contacts/ and crm/preferences.json.
Keep any existing interaction history. Just stop tracking going forward.
```

---

### Unsuppress a contact

```
Unsuppress [NAME or EMAIL] in my Personal CRM and add them to
the [PROJECT NAME] project. Resume tracking going forward.
Update crm/contacts/ and crm/index.json.
```

---

## Part 3: Prompt Quick Reference

| Prompt | Model | When to use |
|---|---|---|
| **SETUP: Initialize CRM** | Opus 4.6 + Extended Thinking | Step 1: once, at the very start |
| **SETUP: Seed contacts (email scan)** | Haiku | Step 2A: scan top 30 email contacts, suppress or track |
| **SETUP: Seed contacts (manual)** | Haiku | Step 2B: name specific people or describe a project to seed directly |
| **SETUP: Initial email sweep** | Haiku / Sonnet for review queue | Step 3: after suppression sweep |
| **SETUP: Signature enrichment** | Haiku | Step 4: after email sweep |
| **SETUP: Meeting notes initial sync** | Haiku | Step 5: optional, if you have a notes tool |
| **SETUP: Schedule daily digest** | Sonnet | Step 6: last step of core setup |
| **SETUP: Schedule morning prep** | Sonnet | Step 7: optional, automated pre-meeting research |
| **Look up a contact** | Haiku | Before any call or meeting |
| **Pre-meeting brief** | Haiku | 30 minutes before a call |
| **Add a manual note** | Haiku | After any unrecorded interaction |
| **Mark a commitment done** | Haiku | When you've completed a commitment |
| **Review a project's contacts** | Haiku | Weekly or monthly project review |
| **Pre-meeting research prep** | Sonnet | On demand: "prep me for today" or "/myday prep" |
| **Add a contact manually** | Haiku | Immediately after meeting someone new |
| **Suppress a contact** | Haiku | When a contact should be excluded |
| **Unsuppress a contact** | Haiku | When reversing a suppression |

---

## Troubleshooting

**The classifier is assigning contacts to the wrong project**
After a digest review session, tell Claude: "Update my CRM preferences based on the corrections I just made." After 3-5 corrections on the same pattern, the classifier stops making that mistake.

**The digest is surfacing too many items**
The first week will be noisier than normal. After the first 10 sessions it should stabilize at 3-6 cards. If it stays high: "Review my CRM preferences and identify any patterns I've consistently dismissed. Apply those as permanent exclusions."

**A contact isn't appearing in myDay**
Check two things: (1) Are they suppressed? If so, use the unsuppress prompt. (2) Did their email get classified with high enough confidence? If not, add them manually.

**Meeting notes aren't syncing**
Run: "Check my [TOOL NAME] connection and list the 5 most recent meeting notes available." If the list is empty or stale, refresh the connection in your Cowork integrations settings.

**A commitment was extracted that isn't real**
Select "Dismiss" in the digest, or: "Review my last 5 extracted commitments and flag any that look like false positives. Ask me to confirm which to dismiss."

**A project needs to be updated or archived**
"Update my CRM config. Change [PROJECT NAME] to: [new description / new domains / archived]." Claude will update crm/config.json and confirm.

---

## File Reference

All CRM files live in your workspace folder under `crm/`.

| File | Contents |
|---|---|
| `crm/config.json` | Project definitions, classification signals, classifier settings |
| `crm/contacts/[email].json` | One file per contact with full profile and relationship metadata |
| `crm/index.json` | Master index of all contacts (name, email, project, last interaction) |
| `crm/interactions.json` | All interaction records (source, date, type, summary) |
| `crm/commitments.json` | All commitments (open, done, snoozed, dismissed) |
| `crm/preferences.json` | Learned classifier preferences built from your digest decisions |

To back up your CRM data, copy the entire `crm/` folder. To reset and start fresh, delete the folder and run the setup prompts again.

---

*Setup takes about 45 minutes once. After that, open the daily digest on your chosen schedule, respond to the cards, and the system runs itself.*
*No expensive CRM subscriptions required.*
