---
name: construction-project-tracker
description: Build a complete construction project schedule and tracker workbook in Excel — 9 tabs (README, Phase Summary with auto-drawing Gantt, Detailed Schedule, Inspections, Payment Draws, Action Items, Project Info, CSI Reference, CSI Sub-Codes) with status dropdowns, row tinting, and self-calculating draw totals, branded to the user's company. Use when starting a new job, setting up a tracker mid-project, or converting a messy schedule into a standard format. Triggers on "new project tracker", "build the schedule for [address]", "set up a job", "production schedule", "project schedule workbook".
---

## When to use
The user wants a production schedule / project tracker for a construction job: a new job, an existing job mid-stream, or a legacy schedule converted to a standard format. Built inside Excel with Claude in Excel, which edits the open workbook directly (tabs, formulas, dropdowns, conditional formatting).

## Inputs (ask all at once, one round)
- **Company name** (used in titles, README, subtitles)
- **Brand primary color** (hex; default navy `#1A3A5C`) and **accent color** (hex; default blue `#4A86E8`). Status colors are fixed and never branded.
- **Project name / address**
- **Client name(s)**, **contract amount**, **deposit** (amount or %), **permit number** — any can be skipped
- **Phase preset**: `Residential addition/remodel`, `Residential new build`, or `Custom` (blank phases)
- **Gantt start Monday** and approximate duration in weeks (default 36)
- **Sources** (all optional): attached files (permit, estimate, plans, soils report, survey, existing schedule), a Slack channel, email search terms, or a typed description of where the job stands

If no sources are given, build the full structure with the preset phase list and leave dates and statuses blank.

## Tokens
- `PRIMARY` = brand primary (titles, header rows, banners text)
- `ACCENT` = brand accent (README banner, README section text, README tab color)
- `PRIMARY_TINT` = very pale tint of PRIMARY for section banners and totals (default `#E8EEF5`)
- `ACCENT_TINT` = very pale tint of ACCENT for README section headers (default `#E8F0FE`)
- `GRID` = `#BDC3C7` · `DARK` = `#434343` (CSI tabs) · `SUBGROUP` = `#D9D9D9`

## Global style frame (every tracker tab: Phase Summary, Detailed Schedule, Inspections, Payment Draws, Action Items)
- **Row 1 — title**: `[Address] — [Tab Purpose]`, bold 14pt Calibri, PRIMARY, no fill.
- **Row 2 — subtitle**: one-line context, italic 10pt `#555555`, thin bottom border GRID across the table width.
- **Row 3 — header**: bold 11pt white on PRIMARY, centered.
- **Body**: Calibri 10pt black. ID, date, and status columns centered; text left; currency right.
- **Borders**: thin solid GRID over the full data range including the header.
- **Freeze**: 3 rows on every tracker tab (Phase Summary also freezes 6 columns).

## Status color system (fixed — never substitute, never brand)
**Saturated (status pills + Gantt bars):** Done `#6AA84F` white text · In Progress `#F1C232` white text · Approximate `#FFE599` black text · Pending `#CCCCCC` black text · Blocked `#E06666` white text.

**Pale row tint, Phase Summary columns A:F:** Done `#D5F5E3` · In Progress `#FCF3CF` · Blocked `#FADBD8` · Pending / Approximate / default `#F4F6F8`.

**Pale row tint, Detailed Schedule / Inspections / Payment Draws:** Done/Passed `#E8F5E0` · Today `#DCE9F7` · In Progress/Scheduled `#FFF4D6` · Blocked/Failed `#FBE0E0` · Pending/future `#F5F5F5`.

**Priority pills (Action Items col B):** 🔴 CRITICAL fill `#FADBD8` text `#922B21` · 🟠 HIGH `#FAE5D3` / `#A04000` · 🟡 MEDIUM `#FCF3CF` / `#7D6608` · 🟢 LOW `#D5F5E3` / `#1E8449` · ✅ DONE `#D5F5E3` / `#1E8449`. Bold 10pt centered.

**Used-on-this-job highlight (CSI tabs):** row fill `#FFF2CC`, bold gold ◀ `#F1C232` in the last column.

## Steps

### 1. Gather context
Ask the inputs in one round. Read whatever sources were given: parse attached files; if a Slack channel was named and a Slack connector is available, read the last ~50 messages; if email terms were given and an email connector is available, search them. If a named connector isn't available, say so in one line and continue with what you have.
Extract phase statuses, dates, inspections, draws, contacts, and open items into a context dictionary.
**Never infer status.** A phase gets a status only when a source states it; otherwise leave it blank (Pending styling applies by default).
**Done when:** context dictionary holds project info, contacts, and every sourced date/status.

### 2. Create the 9 tabs in order
`README`, `Phase Summary`, `Detailed Schedule`, `Inspections`, `Payment Draws`, `Action Items`, `Project Info`, `CSI Reference`, `CSI Sub-Codes`. Tab colors: README = ACCENT; both CSI tabs = DARK; others default.
**Done when:** all 9 sheets exist in order with tab colors.

### 3. README
Column widths A=180, B=480, C=200.
- **Rows 1–2 banner**, fill ACCENT, white text. Row 1 bold 22pt `[Address] — Project Schedule Workbook`; row 2 italic 11pt `[Clients] | [Company] | Read this first.`
- Section headers (bold 13pt ACCENT on ACCENT_TINT, spanning A:C): `WHAT THIS WORKBOOK IS`, `TAB GUIDE`, `HOW THE GANTT CHART WORKS`, `WEEKLY UPDATE WORKFLOW`, `TROUBLESHOOTING`, `BUILT WITH`.
- **TAB GUIDE**: mini-table `Tab | What it does | Who updates it`, header white on DARK, one row per tab, tab names bold.
- **GANTT**: you only touch Status (C), Start (D), End (E) on Phase Summary; bars draw themselves. 5-row legend, each status cell filled with its saturated color, meaning in col B.
- **WEEKLY UPDATE WORKFLOW**: numbered 5-step Monday routine — Phase Summary, Detailed Schedule, Inspections, Payment Draws, Action Items.
- **TROUBLESHOOTING**: bars not showing (dates typed as text; row 2 dates not real dates; row 2 merged past F) · dropdown missing · row colors wrong (never paint by hand) · adding a phase row (insert + copy an existing phase row so formulas and dropdowns carry over).
- **BUILT WITH**: "Built with Claude in Excel using the construction-project-tracker skill."
**Done when:** banner, all six sections, and the colored legend render.

### 4. Phase Summary (dashboard + auto-Gantt)
Column widths A=45, B=290, C=95, D=75, E=75, F=320, G onward=28. Freeze 3 rows + 6 columns.
- **Row 1** title. **Row 2 A:F** subtitle `[Clients] | [Company] | Contract: $X | Permit: Y | Updated [date]`. Row 2 stays **unmerged** from G onward.
- **Row 2, G onward**: real Excel date serials, one per week's Monday for the full duration. Italic 9pt `#808080`, format `m/d`.
- **Row 3**: `CSI Code | Phase | Status | Start | End | Notes`, then `Wk 1\n[m/d]` per week. White on PRIMARY.
- **Phase rows (from 4)**: chosen preset (below) or blank. CSI codes text format `@`. Start/End format `m/d/yyyy` with date validation and input hint "Ctrl+; = today".
- **Status (C)**: dropdown `Done, In Progress, Pending, Blocked, Approximate`; CF fills the cell with its saturated color, bold, centered.
- **Row tint A:F**: CF driven by `$C4` — Done `#D5F5E3`, In Progress `#FCF3CF`, Blocked `#FADBD8`, else `#F4F6F8`.
- **Gantt grid (G4 onward)**, every cell:
  `=IF(AND(ISNUMBER($D4),ISNUMBER($E4),G$2>=$D4,G$2<=$E4),"█","")`
  Bold 11pt centered. CF in priority order (last = highest): Pending → Approximate → In Progress → Done → Blocked, each `=AND(G4<>"",$C4="<status>")`, with **font color = fill color** so the blocks merge into a solid bar.
- **Footer** (2 rows below last phase): `LEGEND:` in A, "(pick from dropdown in column C)" in B, the 5 statuses across C:G as saturated pills. Two rows below: bold `TARGET FINISH: …` highlighted `#FCF3CF` across B:F. Next row, italic gray: `CODE REFERENCE: See the "CSI Reference" tab.`
**Rules:** never type bars by hand. **One trade per line** — each trade's rough and trim get their own rows; never combine finals into one "MEP Trim" row.
**Done when:** dropdown works; a test phase with dates draws a correctly colored bar in the right weeks; label columns tint.

### 5. Detailed Schedule
Columns `Wk | Date Range | Phase | Task/Milestone | Status | Inspection? | Responsible | Notes`; widths 38, 84, 116, 200, 74, 84, 74, 168. A/B/E/F centered; C/D/G/H left. Date Range is text (`@`), e.g. "6/08 – 6/12". Phase entries `[CSI] [Name]`, e.g. "03b Foundation Walls". Status dropdown: `Done, In Progress, Today, Scheduled, Pending, Blocked, Approximate`; full-row tint driven by `$E4`. Inspection column `✓ City` / `—`. MEP trim weeks: one task line per trade with its own Responsible.
**Done when:** Today tints pale blue, Done pale green.

### 6. Inspections
Columns `Inspection | Phase | Date | Result | Engineer Letter? | Inspector / Engineer | Notes`; widths 126, 74, 95, 95, 74, 137, 158. C/D centered. Result dropdown `Passed, Done, Pending, Scheduled, Blocked, Failed, N/A`; row tint by result.
Pre-seed a standard residential sequence: open hole/excavation, footing rebar, footing pour, wall rebar, wall pour, setback/location survey, waterproofing/dampproofing, floor framing, height/bulk plane (if required), rough framing, plumbing rough, mechanical rough, electrical rough, insulation, drywall nail/screw, final/CO. Subtitle row 2: "Adjust to your jurisdiction's required inspections."
**Done when:** dropdown works; rows tint by result.

### 7. Payment Draws
Columns `Draw # | Milestone / Phase | Date | Amount | Status | % of Contract | Notes`; widths 32, 147, 84, 84, 84, 69, 156. Amount `$#,##0.00` right-aligned; % of Contract `=D4/[contract]` formatted `0.00%`. Status dropdown same as Detailed Schedule with matching tint on A:E. Subtitle: `Contract: $X | Deposit: [amount/%] | [draw cadence if known]`.
**TOTALS block** (2 rows below last draw, PRIMARY_TINT banner with border): contract total bold in D, `100.00%` in F; gray 9pt audit rows `Sum of itemized draws` (`=SUM(...)`) and `Balance` (`=[contract]-[sum]`); bold `Received to date (Done)` `=SUMIF(E:E,"Done",D:D)` scoped to the draw rows, fill `#D5F5E3`; `Remaining` `=[contract]-[received]`, fill `#F4F6F8`.
**Done when:** flipping a draw to Done updates Received and Remaining.

### 8. Action Items
Columns `# | Priority | Action / Issue | Owner | Due | Notes / Resolution`; widths 32, 63, 200, 95, 74, 189. No status column — priority pills drive it: dropdown `🔴 CRITICAL, 🟠 HIGH, 🟡 MEDIUM, 🟢 LOW, ✅ DONE` with pill CF. Subtitle: `Active blockers, RFIs, follow-ups. Sorted by priority. Updated: [date]`.
**Done when:** each priority colors its pill.

### 9. Project Info
Two columns; widths A=137, B=402. Title/subtitle per frame, no header row, no borders. Section banners (bold 11pt PRIMARY on PRIMARY_TINT, spanning A:B): `PROJECT INFO`, `CLIENT`, `PROJECT TEAM`, `SCHEDULE METHODOLOGY`, `KEY SPECS`, `SCOPE SUMMARY`, `SUBCONTRACTORS`. Bold 10pt labels in A, values in B, blank row between sections. Pre-fill from context; leave unknowns blank.
**Done when:** seven sections styled; known values filled.

### 10. CSI Reference (division level)
Columns `Div | Division Name | What it covers | Residential? | On [job short name]?`; widths 50, 240, 460, 110, 90. Freeze 4 rows. Tab color DARK. Inner borders thin `#BFBFBF`.
- **Row 1**: `CSI MASTERFORMAT REFERENCE`, bold 14pt white on DARK across A:E.
- **Row 2**: italic 10pt DARK explainer: the number in a schedule code is the CSI division; the letter suffix is this tracker's sub-phase split in build order (03a Footers → 03b Foundation Walls → 03c Dampproof/Backfill → 03d Flatwork). This sheet is in numerical order as a lookup; the schedule tabs run in build sequence. Section-level codes are on "CSI Sub-Codes".
- **Row 4**: header white on DARK.
- **Body**: all 35 active MasterFormat divisions (00–14, 21–28, 31–35, 40–48), grouped under bold-italic SUBGROUP rows: Procurement & Contracting, General Requirements, Facility Construction, Facility Services, Site & Infrastructure, Process Equipment. Div as text. Residential column: ✅ / Sometimes / Rare.
- **Used on this job**: highlight + ◀ in column E. Derive from the phase list actually used; residential default 01, 02, 03, 06, 07, 08, 09, 22, 23, 26 (+12 if countertops, +31 if radon or significant earthwork, +32/33 on new builds). Div 31 description names radon: "…radon mitigation (31 21 13)".
- **Last row legend**: `✅ = Common on most jobs | Sometimes = Job-specific | Rare = Commercial/industrial | ◀ Yellow row = used on THIS job`.
**Done when:** 35 divisions, job rows highlighted, freeze + tab color set.

### 11. CSI Sub-Codes (section level)
Columns `Code | Section Title | Plain English / Typical Scope`; widths 90, 320, 460. Freeze 4 rows; tab color DARK; borders `#BFBFBF`.
- **Row 1**: `CSI SUB-CODES — SECTION-LEVEL LOOKUP`, bold 14pt white on DARK.
- **Row 2**: italic explainer: curated working subset of 6-digit section codes for residential work; Ctrl+F to search; division codes on "CSI Reference".
- **Body**: ~75 curated codes grouped under division header rows (`DIV 03 — CONCRETE`, SUBGROUP fill, bold italic). Cover 01 (temporary facilities, closeout), 02 (existing conditions, demolition, remediation), 03 (forming, reinforcing, cast-in-place, finishing), 04, 05, 06 (rough carpentry, sheathing, trusses, finish carpentry 06 20 00, cabinets 06 41 00), 07 (dampproofing/waterproofing, insulation, weather barriers, roofing, siding, flashing, gutters 07 71 23, sealants), 08 (doors, garage doors 08 36 13, windows, skylights, hardware), 09 (gypsum board 09 29 00, tile, wood/resilient flooring, carpet, paint), 10–14 (incl. countertops 12 36 00), 21 (residential sprinklers), 22 (domestic water, DWV, water heaters, fixtures), 23 (fuel gas piping 23 11 23, ductwork, diffusers/registers, furnaces, condensing units, mini-splits, controls), 25, 26 (wiring, panelboards 26 24 16, devices, lighting), 27, 28, 31 (clearing, 31 21 13 radon mitigation, grading, excavation, fill, erosion control, shoring), 32 (paving, fences, retaining walls, irrigation, planting), 33 (water, sewer, storm services). Highlight radon rows if the job has radon scope.
- Never import the full MasterFormat standard; keep it curated.
**Done when:** grouped, searchable, radon rows present.

### 12. Verify
Screenshot the Phase Summary range. Write a test status + dates into one phase row; confirm the bar appears in the right weeks in the right color and the label columns tint; clear the test. Confirm every tracker tab has title, subtitle, header, borders, and freeze.
**Done when:** test passed and cleared; all 9 tabs consistent.

## Phase presets (Phase Summary rows, in build sequence — not numerical order)

### Residential addition/remodel
| CSI | Phase |
|---|---|
| 01 | General Conditions (permits, dumpster, sanitation) |
| 02 | Site Work — Demolition + Civil Prep |
| 03a | Concrete — Footings |
| 03b | Concrete — Foundation Walls |
| 03c | Dampproof / Underdrain / Backfill |
| 31a | Radon — Slip Sheet + Sub-Slab Vent *(only if radon scope)* |
| 03d | Concrete — Flatwork |
| 06a | Framing — Sills, Floor, Walls |
| 06b | Framing — Roof, Beams, Trusses |
| 06c | Sheathing + Roof Dry-In |
| 08 | Doors & Windows Install |
| 07a | Exterior Siding & Trim |
| 22a | Plumbing Rough |
| 23a | HVAC Rough |
| 26a | Electrical Rough + Panel |
| 07b | Insulation |
| 09a | Drywall — Hang, Tape, Finish, Prime |
| 09b | Tile |
| 09c | Paint |
| 06d | Finish Carpentry — Trim, Baseboard, Interior Doors |
| 06e | Cabinets Install |
| 12 | Countertops |
| 09d | Flooring |
| 22b | Plumbing Trim (fixtures) |
| 23b | HVAC Trim + Startup |
| 26b | Electrical Trim (devices, plates, fixtures) |
| CLOSE | Punch + Final Inspection + CO |

### Residential new build
Same as above, with these changes: replace 02 with `31b Excavation + Grading`; add `33 Utilities — Water, Sewer, Gas, Electric Service` after 31b; add `07c Roofing` after 06c; add `32 Driveway, Flatwork, Landscape` before CLOSE.

### Custom
Leave phase rows blank with formulas, dropdowns, and formatting in place for at least 30 rows.

**MEP is always three separate rough rows and three separate trim rows.**

## Adding or splitting a phase mid-job
1. Insert the row at its build-sequence position.
2. Copy an adjacent phase row across the full width (A through the last week column), all formatting and formulas — this carries tint, date formats, dropdown, and the row-relative Gantt formulas.
3. Overwrite code, phase, status, dates, notes.
4. Verify a bar draws in the correct weeks before reporting done.
Same recipe on Detailed Schedule (copy A:H).

## Performance
Set calculation to manual before applying conditional formatting and validation across tabs; restore automatic at the end. Build one tab per step so the user sees progress.
