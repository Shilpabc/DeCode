# De-Code

Config-driven Claude Skills for manufacturing, sales, and knowledge-work
automation. Every skill here is genericized — no client data, no live IDs — and
built to be pointed at *your own* Notion workspace (or other backend) during a
one-time setup step.

## Skills in this repo

| Skill | Folder | What it does |
|---|---|---|
| **Enquiry Desk** | [`Enquiry-Desk/`](./Enquiry-Desk) | Full enquiry-to-delivery pipeline in Notion — logging, status lifecycle, follow-up queue, pipeline analytics |
| **Purchase Registry** | [`Purchase-Registry/`](./Purchase-Registry) | Purchase order CRUD, status tracking, supplier spend, overdue reporting |
| **Registry Management** | [`Registry-Management/`](./Registry-Management) | Generic register builder — scaffolds a brand-new Notion register type from a description, for anything that doesn't have a dedicated skill yet |
| **Heat Chart Generation** | [`Heat-Chart-Generation/`](./Heat-Chart-Generation) | Populates a material heat/traceability chart from extracted BOM data |
| **BOM Extraction** | [`BOM-Extraction/`](./BOM-Extraction) | Parses engineering/technical drawings (any discipline) into structured JSON — BOM, title block, schedules, revisions |
| **Market Research** | [`Market-Research/`](./Market-Research) | Turns a one-line business description into an executive brief, ICP, target list, and outreach playbook |

## Installation

Each folder is self-contained: copy it into your Claude Skills directory as-is
(`<skill-name>/SKILL.md`). Each one also has its own `README.md` with
setup-specific instructions — several require a one-time Notion connection
before first use, and will prompt you for it rather than run against
placeholder data.

## Status

All skills here are config-driven and have had client-specific identifiers
(workspace IDs, company names, internal numbering schemes) removed and
replaced with setup placeholders. If you find a leftover reference that
shouldn't be there, open an issue.

## License

Use, adapt, and remix freely. A link back is appreciated but not required.
