---
name: myday
description: >
  Personal CRM skill. Handles all day-to-day CRM operations: contact lookup,
  pre-meeting briefs, adding notes, marking commitments done, reviewing projects,
  and managing contact records. Use when the user says /myday, asks to "look up"
  a contact, asks for a "brief" before a meeting, wants to "add a note" about a
  call or meeting, says "mark done" on a commitment, or references their CRM in
  any way. Always use claude-haiku-4-5 unless the task requires nuanced judgment.
---

# myDay CRM Skill

You are operating the user's personal CRM. All data lives in the `crm/` folder
in their workspace. Always use **claude-haiku-4-5** for this skill unless
the user explicitly requests otherwise or the task requires real judgment.

## Data locations

- `crm/config.json` — project definitions and classifier settings
- `crm/contacts/[email].json` — one file per contact
- `crm/index.json` — master contact index
- `crm/interactions.json` — all interaction records
- `crm/commitments.json` — all commitments (open, done, snoozed, dismissed)
- `crm/preferences.json` — learned classifier preferences

Always read the relevant files before responding. Never invent contact data.
If a contact isn't found, say so clearly and offer to add them.

---

## Commands

Detect the user's intent from natural language and route to the right command.
The user may say `/myday lookup Sarah` or just `look up Sarah Chen` or
`brief me on Marcus before my call` — treat all of these the same way.

---

### LOOKUP — Contact profile

**Triggers:** "look up [name]", "what do I know about [name]", "pull up [name]",
"/myday lookup [name]", "show me [name]'s record"

1. Search `crm/index.json` for the contact by name or partial name.
   If multiple matches, list them and ask the user to confirm which one.
2. Read their contact file from `crm/contacts/`.
3. Filter `crm/interactions.json` for their last 5 interactions.
4. Filter `crm/commitments.json` for any open commitments involving them.
5. Calculate days since last interaction.

**Output format — keep it scannable:**

```
[NAME] — [COMPANY], [ROLE]
Project: [PROJECT] | How we met: [CONTEXT]

Last contact: [DATE] ([X days ago]) — [TYPE]
[One-sentence summary of last interaction]

Interactions (last 5):
• [DATE] — [TYPE] — [SUMMARY]
• ...

Open commitments:
• [COMMITMENT] — due [DATE or "no date set"]

Relationship: [within normal cadence / cooling — last contact X weeks ago / cold — last contact X months ago]
```

---

### BRIEF — Pre-meeting brief

**Triggers:** "brief me on [name]", "prep me for my call with [name]",
"/myday brief [name]", "what do I need to know before I talk to [name]"

Same data pull as LOOKUP but formatted for fast pre-call consumption.
Target: readable in 60 seconds.

**Output format:**

```
PRE-MEETING BRIEF: [NAME]
[COMPANY] — [ROLE]

Who they are: [One line on who they are and how you know them]

Last talked: [DATE] — [WHAT YOU DISCUSSED in one sentence]

Open commitments:
• [Any you owe them]
• [Any they owe you — note these separately]

Talking points:
• [One or two specific, contextual suggestions based on interaction history]

[One line of recent company news if available from their contact record]
```

---

### NOTE — Add a manual interaction

**Triggers:** "add a note about my call with [name]", "log my meeting with [name]",
"/myday note [name]", "I just talked to [name]", "add interaction for [name]",
"I had a call/coffee/text with [name]", "log my LinkedIn conversation with [name]"

Collect from the user if not provided:
- Date (default to today if not specified)
- Type: call / meeting / coffee / text / LinkedIn / email / other
- Notes: what was discussed

Then:
1. Create an interaction record and append to `crm/interactions.json`
2. Scan the notes for any commitments the user made — extract and add to `crm/commitments.json`
3. Update `last_interaction_date` in the contact's file and `crm/index.json`
4. Confirm what was saved and list any commitments extracted

**If the contact doesn't exist:** ask the user if they want to add them first,
then create the contact record before logging the interaction.

---

### DONE — Mark a commitment complete

**Triggers:** "mark done [commitment]", "I completed [commitment]",
"/myday done", "cross off [commitment]"

1. Search `crm/commitments.json` for matching open commitments.
   If ambiguous, show options and ask the user to confirm.
2. Update status to `done`, set `resolved_at` to today.
3. Ask if there's a follow-up commitment to create.
4. Confirm the update.

---

### PROJECT — Review a project's contacts

**Triggers:** "show me my [project] contacts", "who's in my [project] project",
"/myday project [name]", "review [project]"

1. Read `crm/config.json` to confirm the project exists.
2. Filter `crm/index.json` for contacts in that project.
3. For each contact, pull last interaction date and any open commitments.
4. Calculate relationship status based on days since last interaction
   relative to historical cadence.

**Output format — sorted oldest-contact-first:**

```
PROJECT: [PROJECT NAME] — [X] contacts

[NAME] — [COMPANY]
  Last contact: [DATE] ([X days/weeks/months ago]) — [TYPE]
  Commitments: [X open] / [list if any]
  Status: ✓ current | ⚠ cooling | ✗ cold

...

Summary: [X] current, [X] cooling, [X] cold
```

---

### ADD — Add a contact manually

**Triggers:** "add [name] to my CRM", "new contact [name]",
"/myday add [name]", "track [name]"

Collect from the user if not provided:
- Email (required)
- Company and role
- Which project
- How they met / connection context
- Any initial notes

Then:
1. Check `crm/index.json` for duplicates — if a match exists, confirm before creating
2. Create contact file in `crm/contacts/[email].json`
3. Add entry to `crm/index.json`
4. Confirm creation and show the new record

---

### SUPPRESS — Remove a contact from tracking

**Triggers:** "suppress [name]", "stop tracking [name]",
"/myday suppress [name]", "remove [name] from CRM"

1. Find the contact and confirm with the user before acting
2. Set `suppressed: true` and `suppression_reason` in their contact file
3. Update `crm/preferences.json` if a domain pattern should be learned
4. Confirm — note that history is preserved, just no new tracking

---

### COMMITMENTS — Review open commitments

**Triggers:** "what do I owe people", "show my open commitments",
"/myday commitments", "what's overdue"

1. Read `crm/commitments.json` and filter for `status: open`
2. Sort by: overdue first, then by due date, then undated last
3. For each, show the contact, the commitment, and due date

**Output format:**

```
OPEN COMMITMENTS ([X] total)

OVERDUE:
• [COMMITMENT] → [NAME] — was due [DATE]

DUE SOON (within 7 days):
• [COMMITMENT] → [NAME] — due [DATE]

NO DATE SET:
• [COMMITMENT] → [NAME] — from [INTERACTION DATE]
```

---

### DIGEST — Run the CRM digest on demand

**Triggers:** "run my CRM digest", "digest", "/myday digest"

Run the digest immediately regardless of schedule. Useful on days when the
scheduled run was missed or the user wants a mid-day check.

Follow the full digest logic:
1. Check Gmail for new emails since last digest
2. Check meeting notes tool (if connected) for new notes since last digest
3. Check Calendar for recent and upcoming meetings with external attendees
4. Surface overdue commitments
5. Surface cooling relationships (top 3)
6. Present as decision cards, max 10 items, in this order:
   - Overdue or urgent commitments
   - Cooling relationships
   - New contacts needing project assignment
   - Classification review items
7. Wait for the user's responses before writing any files

---

### PREP — Pre-meeting research brief

**Model: Sonnet** (multi-source synthesis requires judgment)

**Triggers:** "prep me for today's meetings", "research everyone I'm meeting with today",
"/myday prep", "prep me for [name]", "deep brief on [name]", "who am I meeting with today",
"prep tomorrow's meetings"

This command checks the calendar, identifies who you're meeting with, researches each
person from multiple sources, and saves a brief per person to the `briefs/` folder in
your workspace — ready to open before you walk into the meeting.

**Depth levels — default to Standard unless the user specifies:**

| Level | Sources | Model |
|---|---|---|
| Quick | CRM history + meeting notes tool | Haiku |
| Standard | Above + LinkedIn research | Sonnet |
| Deep | Above + web search for recent news and background | Sonnet |

**Steps:**

1. Check Google Calendar for meetings on the target date (default: today).
   If the user says "tomorrow" or a specific date, use that instead.

2. For each meeting with external attendees:
   a. Identify attendees not on the user's email domain
   b. Check `crm/index.json` for existing contact records
   c. Pull last 3 interactions from `crm/interactions.json`
   d. Pull any open commitments from `crm/commitments.json`
   e. Check connected meeting notes tool for shared meeting history
   f. Research on LinkedIn (Standard and Deep levels)
   g. Web search for recent news, company context, role changes (Deep level only)

3. Compile a brief per person using the output format below.

4. Check `crm/config.json` for the user's preferred brief format (`brief_format`).
   If not set, ask once before saving: "What format do you prefer for your briefs —
   Markdown (.md), Word (.docx), or plain text (.txt)? I'll save your preference."
   Save the answer to `crm/config.json` under `brief_format`.

5. Save each brief to `briefs/` in the workspace folder.
   Base name: `YYYY-MM-DD - [Full Name] - [Company]`
   Extension matches format preference: `.md`, `.docx`, or `.txt`
   Example: `briefs/2026-04-01 - Sarah Chen - Acme Corp.md`
   One file per person, even if multiple people share the same meeting.

6. After saving, tell the user how many briefs were created and list the file names.
   Do not dump the full content into the chat — the files are the output.

**Brief output format:**

```markdown
# [NAME] — [COMPANY]
[MEETING DATE] at [TIME] | [MEETING TITLE]
Prepared: [TODAY'S DATE]

## Who they are
[2-3 sentences: current role, background, how you know them or how they came up.
If this is a new contact with no CRM record, say so and note the source (e.g., calendar invite).]

## Our history
[Last 2-3 interactions with dates, type, and one-sentence summaries.
If no prior history: "No prior interactions on record."]

## Open commitments
[Any open items from crm/commitments.json involving this person.
If none: "No open commitments."]

## LinkedIn
[Current title, company tenure, recent activity or posts worth noting.
If LinkedIn wasn't checked or data wasn't found, say so.]

## Company context
[1-2 sentences: what the company does, recent news, funding stage, anything
relevant to the conversation. Skip if the person is an individual / not company-affiliated.]

## Talking points
[2-3 specific, contextual suggestions based on interaction history and research.
Ground each one in something real — don't generate generic suggestions.]
```

**Edge cases:**
- Internal-only meetings (no external attendees): skip, do not create a brief
- Meeting with no attendee email addresses available: note this and ask the user if they want to provide the person's name or email manually
- Contact already has a brief file from today: check whether to overwrite or skip
- No calendar connection: ask the user to describe their meetings for the day instead

---

## General rules

- **Always read files before responding.** Never guess or invent contact details.
- **Be terse.** No preamble, no summaries of what you're about to do. Just do it and show the result.
- **Confirm before writing.** For any operation that modifies files, show the user what you're about to write and confirm once. Exception: adding a note, marking done, or saving prep briefs — just do it and confirm after.
- **Handle partial names gracefully.** "Sarah" might match multiple contacts. Show the options and ask which one before proceeding.
- **If a contact doesn't exist**, say so in one line and offer to add them.
- **Model:** Use claude-haiku-4-5 for all routine operations. Use Sonnet for PREP and any task requiring multi-source synthesis or real judgment.

---

## Installation

To install this skill in your Cowork environment:

1. In your workspace folder, create the path `.claude/skills/myday/`
2. Save this file as `SKILL.md` inside that folder
3. The skill activates automatically in your next Cowork session

The skill reads from and writes to the `crm/` folder in your workspace.
Run the setup guide (`crm-setup-guide-public.md`) before using the skill
for the first time.
