---
name: register-management
description: >
  Generic register builder and manager for manufacturing/industrial registers in Notion —
  use this when the user wants to create a NEW type of register that doesn't already have
  a dedicated skill (e.g. Customer Master, Equipment/Asset Register, Drawing Register,
  Subcontractor/Vendor Register, Tooling Register, Calibration Register, Non-Conformance
  Register, Audit Register, or any other tabular tracking register for a manufacturing
  business). Triggers: "create a new register for X", "I need a register to track Y",
  "build a register management skill", "set up a [X] register", "register builder", or any
  request to define, scaffold, and operate a brand-new register type from scratch. Do NOT
  use this for a business's existing PO/Material/Work Order/Enquiry registers — those have
  their own dedicated skills. ALWAYS use this skill when the register type is new/undefined
  and doesn't match an existing dedicated registry skill.
---

# Register Management Skill

A two-phase generic register builder: **define** a register's schema once via a config
file, then **operate** it (create/read/update/bulk import/dashboard) using that config —
without hardcoding any field names into this skill itself.

This skill exists because every register (materials, customers, equipment, drawings...)
needs the same workflow shape — title field, status workflow, CRUD, bulk import, query,
dashboard — but a different field list. Hardcoding fields per register (like the earlier
one-off registry skills in this repo do) doesn't scale to "any register the user dreams
up." This skill instead
reads field definitions from a config file and drives every Notion operation off it.

---

## Phase 1 — DEFINE a new register

Trigger: "create a new register for ___", "I need to track ___".

### Step 1: Interview the user

Don't ask for a field list cold — propose one based on what the register is for, then let
the user edit it. Use `ask_user_input_v0` only if the register's purpose itself is unclear;
if the user already named the entity (e.g. "customer register", "equipment register"), skip
straight to proposing fields.

Reference templates for common manufacturing registers (use as a starting point, adapt freely):

**Customer Master Register**
| Field | Type | Notes |
|---|---|---|
| Customer Name | title | required |
| Customer Code | text | internal short code |
| GSTIN | text | |
| Category | select | OEM / Trader / Government / Export |
| Credit Terms (Days) | number | |
| Credit Limit | number | format dollar/rupee |
| Status | status | Active / On Hold / Blacklisted |
| Primary Contact | text | |
| Contact Phone | phone_number | |
| Contact Email | email | |
| Region | select | |
| Remarks | text | |

**Equipment / Asset Register**
| Field | Type | Notes |
|---|---|---|
| Asset Name | title | required |
| Asset Tag No | text | unique tag/serial |
| Asset Type | select | Crane / Welding Machine / Press / Forklift / Instrument |
| Location | text | shop floor / bay |
| Status | status | In Use / Idle / Under Maintenance / Decommissioned |
| Calibration Required | checkbox | |
| date:Last Calibration:start | date | |
| date:Next Calibration Due:start | date | |
| Owner Department | select | |
| Purchase Value | number | |
| Remarks | text | |

**Drawing Register**
| Field | Type | Notes |
|---|---|---|
| Drawing Title | title | required |
| Drawing No | text | |
| Revision | text | A, B, C... |
| Discipline | select | Mechanical / Civil / Electrical / Piping |
| Status | status | Draft / Issued for Review / Approved / Superseded |
| Job No | text | |
| date:Issue Date:start | date | |
| Approved By | text | |
| Remarks | text | |

**Subcontractor / Vendor Approval Register**
| Field | Type | Notes |
|---|---|---|
| Vendor Name | title | required |
| Vendor Code | text | |
| Scope of Work | select | Machining / Rolling / Forming / NDT / Painting |
| Approval Status | status | Approved / Conditional / Rejected / Under Review |
| Rating | select | A / B / C |
| date:Last Audit Date:start | date | |
| Contact Person | text | |
| Remarks | text | |

For anything outside these four, ask the user to list the fields they care about, or offer
to infer fields from a sample Excel/CSV if they have one.

### Step 2: Confirm the schema with the user

Present the proposed field list as a table and ask for changes before building anything in
Notion. Identify:
- The **title field** (every Notion DB needs exactly one — usually "[Entity] Name")
- The **status field** values (if there's a workflow)
- Which fields are **required** vs optional

### Step 3: Write the config file

Save to `registers/{register-slug}-register.json` in this skill's working copy (and also to
`/mnt/user-data/outputs/` so the user has a durable copy). Schema:

```json
{
  "register_name": "Customer Master Register",
  "slug": "customer-master",
  "title_field": "Customer Name",
  "status_field": "Status",
  "status_values": ["Active", "On Hold", "Blacklisted"],
  "fields": [
    {"name": "Customer Name", "type": "title", "required": true},
    {"name": "Customer Code", "type": "text", "required": false},
    {"name": "GSTIN", "type": "text", "required": false},
    {"name": "Category", "type": "select", "options": ["OEM", "Trader", "Government", "Export"]},
    {"name": "Credit Terms (Days)", "type": "number"},
    {"name": "Status", "type": "status", "options": ["Active", "On Hold", "Blacklisted"]},
    {"name": "Primary Contact", "type": "text"},
    {"name": "Contact Phone", "type": "phone_number"},
    {"name": "Contact Email", "type": "email"},
    {"name": "Region", "type": "select"},
    {"name": "Remarks", "type": "text"}
  ],
  "notion": {
    "database_id": null,
    "data_source_id": null,
    "database_url": null,
    "default_view_url": null
  },
  "created": "YYYY-MM-DD"
}
```

`notion.*` fields stay `null` until Phase 2 creates the actual database, then get filled in
and the file gets re-saved. This is what lets a future session find the register again.

### Step 4: Maintain the register index

Keep `registers/_index.json` listing every register created so this skill (and any future
session) can answer "what registers exist?" without re-scanning Notion:

```json
{
  "registers": [
    {"slug": "customer-master", "name": "Customer Master Register", "config": "registers/customer-master-register.json"}
  ]
}
```

Append to this on every new register; never overwrite existing entries.

---

## Phase 2 — BUILD: scaffold the Notion database

Once the config is confirmed, translate `fields[]` into a DDL schema for
`Notion:notion-create-database`. Type mapping:

| Config type | DDL type |
|---|---|
| title | TITLE |
| text | RICH_TEXT |
| number | NUMBER |
| select | SELECT('opt1':color, 'opt2':color, ...) |
| multi_select | MULTI_SELECT('opt1':color, ...) |
| status | STATUS — or SELECT if simple workflow without Notion's native status groups |
| date | DATE |
| checkbox | CHECKBOX |
| email | EMAIL |
| phone_number | PHONE_NUMBER |
| url | URL |
| people | PEOPLE |

Assign colors round-robin (blue, green, yellow, orange, red, purple, pink, gray) across
select/status options for visual distinction — don't leave everything default gray.

Example call shape:

```
Notion:notion-create-database
  title: "Customer Master Register"
  schema: CREATE TABLE (
    "Customer Name" TITLE,
    "Customer Code" RICH_TEXT,
    "GSTIN" RICH_TEXT,
    "Category" SELECT('OEM':blue, 'Trader':green, 'Government':yellow, 'Export':purple),
    "Credit Terms (Days)" NUMBER,
    "Status" SELECT('Active':green, 'On Hold':yellow, 'Blacklisted':red),
    "Primary Contact" RICH_TEXT,
    "Contact Phone" PHONE_NUMBER,
    "Contact Email" EMAIL,
    "Region" RICH_TEXT,
    "Remarks" RICH_TEXT
  )
```

After creation, the tool response includes the data source ID (in a `<data-source>` tag) —
capture it and the database URL, write them into the config's `notion` block, and re-save
the config file. Confirm with the user: show the Notion URL and the final field list.

Optionally create a default table view and a status-grouped board view with
`Notion:notion-create-view`.

---

## Phase 3 — OPERATE: CRUD against an existing register

Once `notion.data_source_id` is populated in a config, every operation below reads field
names/types from that config — never hardcode a field name in skill logic itself.

### CREATE — single entry

1. Load the register's config file to get `data_source_id`, `title_field`, required fields.
2. Use `Notion:notion-create-pages` with `parent.data_source_id` from config.
3. If the title field value isn't given, ask for it — it's always required.
4. Validate any `select`/`status` value against `options` in config; if it doesn't match,
   ask the user to confirm a close match or add it as a new option via
   `Notion:notion-update-data-source` (`ADD COLUMN` won't work for adding a select option —
   use `ALTER COLUMN "Field" SET SELECT(...)` with the full updated option list).

### CREATE — bulk import from Excel/CSV

1. Parse the uploaded file (`pandas`/`openpyxl`, `pip install --break-system-packages`).
2. Fuzzy-match column headers to `fields[].name` from config (case-insensitive, ignore
   punctuation/whitespace differences).
3. Show a preview: rows to import, column mapping, any unmapped columns (route to a
   free-text field if one exists, e.g. "Remarks", else flag as dropped).
4. Confirm with the user before writing.
5. Batch via `Notion:notion-create-pages`, ≤20 pages per call.
6. Report: ✅ created | ⚠️ skipped, with reasons.

### READ / QUERY

Use `Notion:notion-query-database-view` (default view from config) or
`Notion:notion-search` with `data_source_url: "collection://{data_source_id}"` for
keyword/semantic search. Always present results as a table using the field names from
config — don't invent display columns.

### UPDATE — single entry

`Notion:notion-update-page` against the page ID found via search/query. Re-validate any
status/select changes against config `options`.

### UPDATE — schema evolution

If the user wants to add/remove/rename a field after the register is live, use
`Notion:notion-update-data-source` with the appropriate DDL statement, **and** update the
local config file's `fields[]` to match — the two must never drift apart, since every
future operation trusts the config over Notion's live schema.

### DASHBOARD

For "show me a dashboard/summary of [register]": query the data source, group by the
status field, present counts per status plus any obviously useful aggregate (e.g. total
credit limit exposure, count by category). Offer to create a Notion board view grouped by
status if one doesn't exist yet.

---

## Error handling

- **No config found for a register the user references**: search `registers/_index.json`
  first; if truly absent, treat as a new register request (Phase 1).
- **Config and Notion schema disagree** (e.g. someone edited the Notion DB directly): trust
  Notion as ground truth for structure, but flag the mismatch to the user and offer to sync
  the config file.
- **Ambiguous register type request**: don't guess a field list silently — propose one and
  get confirmation (Phase 1, Step 2) before creating anything in Notion. Creating a database
  is harder to undo than asking one clarifying question.
- **User wants a register that's basically PO/Material/WO/Enquiry with a new name**: point
  them to the existing dedicated skill instead of duplicating it here.

---

## Files in this skill

```
register-management/
├── SKILL.md
└── registers/
    ├── _index.json              — list of all registers created
    └── {slug}-register.json     — one config per register (created on demand)
```
