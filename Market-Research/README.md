[README.md](https://github.com/user-attachments/files/29677551/README.md)
# Market Research Skill

A Claude Skill that turns a one-line business description into a structured, decision-ready market research package: an executive brief, a tiered ICP, a named target list with contact intelligence, and (for deeper runs) an outreach playbook and 30-day action plan — plus a print-ready HTML file, generated automatically every time.

Built to think like a **Growth Marketer + Market Analyst + SDR Manager** in one pass. No fluff, no filler bullets, tables over paragraphs.

---

## What it produces

| Depth | Output |
|---|---|
| **Executive Summary** | Executive Brief only (market stat, 3 actions, top 50 targets) |
| **High-Level** | Executive Brief + Ideal Customer Profile (3 tiers) |
| **Deep Dive** | Executive Brief + ICP + Outreach Playbook (LinkedIn/email/content/partnerships) + 30-Day Action Plan |

Every run also generates a standalone, print-formatted HTML file of the report, saved and shared automatically — you never have to ask for it separately.

---

## Installation

1. Copy this folder (`market-research/`) into your Claude Skills directory:
   - **Claude.ai / Claude Code**: place it under your skills folder as `market-research/SKILL.md`
   - **Project-level**: drop it into your project's skills directory if you're scoping it to one workspace
2. No dependencies, API keys, or config files required — it's a single markdown instruction file.

---

## How to use it

Just describe what you're trying to do. You don't need to name the skill — trigger phrases like these will invoke it automatically:

- "Research my market"
- "Help me find clients for [your business]"
- "Build an ICP for [industry/geography]"
- "Who should I target for [business goal]?"
- "Create a lead gen strategy for [product/service]"
- "Build me an outreach playbook for [target audience]"

### Inputs it needs

The skill will ask for these if they're not already clear from your message:

| Parameter | Required? | Example |
|---|---|---|
| Industry | Yes | "Industrial Manufacturing", "SaaS", "EdTech" |
| Geography | Yes | "UAE", "Bengaluru", "US Midwest" |
| Target Audience | Yes | "Mid-size manufacturers, 100–150Cr revenue" |
| Business Goal | Yes | "Market expansion", "lead generation", "AI transformation pitch" |
| Competitor Set | No | Named competitors, or leave blank |
| Research Depth | Yes | Executive Summary / High-Level / Deep Dive |

**Tip:** the more specific your Target Audience and Business Goal, the sharper the output — "manufacturers" produces a generic report, "process equipment manufacturers, ₹100–150Cr revenue, evaluating AI-led quality control" produces a usable one.

### Example prompt

> "Deep dive on Indian SME process equipment manufacturers, ₹100–150Cr revenue, for a GenAI productivity consulting pitch. No named competitors yet."

This alone is enough — the skill extracts what it can and asks only for what's missing.

---

## What you'll get back, in order

1. **Executive Brief** — one market-opportunity line with 3 stats, 3 same-day actions, and a top-50 target table (company, why-now, contact name/title/email pattern/LinkedIn search string, confidence tag)
2. **ICP table** (High-Level and Deep Dive) — 3 tiers across industry, size, revenue, geography, tech stack, buying trigger, budget authority, cycle length
3. **Outreach Playbook** (Deep Dive only) — LinkedIn filters + connection note + 3-touch DM sequence, cold email sequence with A/B subject lines, 3 content ideas, up to 3 partnership plays
4. **30-Day Action Plan** (Deep Dive only) — week-by-week focus, top 3 actions, KPI
5. **Downloadable HTML report** — styled, print-ready (A4), generated and delivered automatically

---

## Notes on contact intelligence

The skill never fabricates names. Every contact row is tagged:

- ✅ **Verified** — name confirmed via official source (site, press release, news)
- 🟡 **Inferred** — public name, email inferred from known domain pattern
- 🔍 **Locate via tool** — role confirmed, name not found; a LinkedIn search string is provided instead

Named companies and competitors must be real, verifiable businesses — the skill will label anything hypothetical as an "archetype" rather than presenting it as a real entity.

---

## License / attribution

Use, adapt, and remix freely. If you build on this, a link back is appreciated but not required.
