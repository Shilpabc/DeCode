[README.md](https://github.com/user-attachments/files/29677523/README.md)
# Enquiry Desk Skill

A Claude Skill that runs a full sales-to-delivery enquiry pipeline inside Notion —
built for manufacturing, engineering, and fabrication businesses that track
enquiries from first customer contact through quotation, order, production, and
dispatch.

This skill does **not** come wired to any Notion workspace. It ships with
placeholder configuration on purpose — you connect it to your own Notion
database during setup, and it refuses to run against guessed or example IDs.

---

## What it does

- Logs new enquiries with auto-numbered IDs
- Updates status through a defined lifecycle: New Inquiry → Quotation Sent →
  Negotiation → Order Confirmed → Engineering → Procurement → Production → QC →
  Dispatched → Delivered (or Lost NFA / On Hold at any stage)
- Runs a follow-up queue with urgency tiers
- Calculates pipeline value, weighted pipeline, conversion rate, and average
  days to close
- Searches enquiries by number, customer, or product

---

## Prerequisites

1. A **Notion account** with permission to create a database.
2. The **Notion connector enabled** in Claude (Settings → Connectors → Notion,
   or your workspace admin has it turned on). Without this, the skill can read
   your instructions but can't touch your actual data.

---

## Setup

### Step 1 — Create your Enquiry Register in Notion

Create a new Notion database (or duplicate an existing one) with the fields
listed in the **Full Database Schema** section of `SKILL.md`. At minimum, the
**Core Sales Fields** table needs to exist for the skill to work at all — the
Quotation, Order, Engineering/Production, and Dispatch field groups are only
needed if your business tracks those stages.

If you don't do in-house fabrication, skip the Engineering & Production Fields
entirely and tell Claude to stop the lifecycle at Order Confirmed → Dispatched
→ Delivered (see the note at the bottom of the schema section in `SKILL.md`).

### Step 2 — Set your numbering scheme

Decide on:
- A short prefix for enquiries (e.g. `INQ`)
- A company code for quotations (e.g. your initials or short name)
- A period code (fiscal year, quarter — whatever your business uses, e.g. `FY26`)

Fill these into the **Numbering Scheme** table in `SKILL.md`.

### Step 3 — Get your Notion IDs

- Open your Hub Page (or workspace landing page) in Notion → copy its URL.
- Open your Enquiry Register database → copy its URL.
- The **Data Source ID** is the UUID in the database URL (the segment after
  the last `/` and before any `?`). If you're unsure which value this is, just
  give Claude the database URL — it can resolve the ID for you via the Notion
  connector.

### Step 4 — Drop the skill in

Place this folder in your Claude Skills directory as `enquiry-desk/SKILL.md`
(plus this `README.md` alongside it for your own reference — Claude only reads
`SKILL.md`).

---

## What happens the first time you use it

You don't need to manually edit `SKILL.md` before trying it — the skill is
built to catch its own unconfigured state:

1. You ask Claude something like *"log a new enquiry from Acme Corp"*.
2. Claude checks the **Configuration** table at the top of `SKILL.md`. If it's
   still placeholders (`<YOUR_HUB_PAGE_URL>`, etc.), it will **not** guess or
   proceed.
3. Claude will instead:
   - Ask you to connect Notion, if it isn't connected yet.
   - Ask for your Hub Page URL, Enquiry Register URL, and prompt to resolve
     the Data Source ID from that URL.
   - Write those values into the Configuration table so you're only asked once.
4. From then on, it runs normally — no repeated setup prompts, unless a Notion
   call fails with a not-found or permission error (which usually means an ID
   changed or access was revoked, at which point it will ask again rather than
   fail silently).

If you'd rather configure it upfront instead of being prompted, just fill in
the Configuration table in `SKILL.md` yourself before first use.

---

## Usage

Trigger it naturally — you don't need to name the skill:

- "Log a new enquiry from [customer], they want [product]"
- "Update INQ-FY26-012 to Quotation Sent"
- "What needs follow-up today?"
- "Show me the pipeline"
- "Mark this as order confirmed, PO number [X]"
- "What's our conversion rate this quarter?"

---

## Customizing for your business

Two sections in `SKILL.md` are explicitly meant to be edited, not used as-is:

- **Numbering Scheme** — your prefix, company code, and period code
- **Domain Intelligence** — replace the placeholder sectors and watch-outs
  with the ones relevant to your industry (certifications, regulated
  customers, sub-contracted process risks, typical payment terms)

Everything else (schema, status lifecycle, workflows) works as a general
enquiry-to-delivery pipeline, but feel free to trim stages you don't need.

---

## License / attribution

Use, adapt, and remix freely. If you build on this, a link back is appreciated
but not required.
