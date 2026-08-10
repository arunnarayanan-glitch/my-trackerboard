# Trackerboard Configuration — Session Summary

Context-recovery doc for continuing this work in a new chat. Covers what was built, why, and what's still open.

## Goal

Turn `Trackerboard_Configuration_Blueprint.md` (Dashboard + Create New Trackerboard, 5 config sections) into a wireframe to show the team the plan. Started as a "pure black and white, low-effort skeleton" and evolved into a high-fidelity mockup once a design-system reference was introduced.

## Files in `v4/`

| File | Status |
|---|---|
| `Trackerboard_Configuration_Blueprint.md` | Source spec — unchanged, still the reference doc |
| `Trackerboard_Skeleton_Wireframe.html` | First pass: B&W, low-fidelity, single HTML, two screens toggled by JS. Superseded but kept. |
| `Trackerboard_HighFidelity_Mockup.html` | **Current primary deliverable.** Styled, interactive, single self-contained HTML file. |
| `Blueprint V2.2 TB.md`, `Solution V2.2 TB.md`, `Wireframe V2.2 TB.html` | An earlier, unrelated, more complex design (drawer editor, roster widgets, permissions matrix, Draft→Preview→Active→Inactive lifecycle). Explicitly **set aside** — user said "ignore V2.2 entirely." Never merged into current work. Still sitting in the folder; candidate for archiving later. |

External reference (not part of this module, do not confuse):
`D:\Claude\Trackerboard_Configuration.html` — a separately-located, fully-styled prototype whose **CSS only** (navy/blue design system: `.btn`, `.card`, `.table`, `.pill`, `.drawer`, `.backdrop`, `.status-item`, `.check-grid`, etc.) was copied verbatim into the mockup. Its own markup/JS were deliberately *not* read or copied — user said "use the CSS for now, we can update later."

## How we got here (chronological)

1. **Analysis + scoping questions** on the blueprint before writing anything (per user's standing "investigate before acting" preference). Key decisions locked in: ignore V2.2, one HTML file, Dashboard→Create navigation via a button, abstract settings only for Widgets/Patient Table (no live-preview mockups), fold "Care Setting-Specific Columns" and "Expanded Row Fields" into the Patient Table section, footer actions = Cancel / Save as Draft / Activate.
2. Built the **B&W skeleton wireframe**.
3. Feedback: too cramped → reworked typography/whitespace (bigger type scale, more spacing, restructured the status rows which were the worst offender).
4. Asked to rebuild in **high fidelity** using the external reference file's CSS. Copied the `<style>` block byte-for-byte (see *Technical note* below for why this had to be done via shell commands, not by hand-copying displayed text). Rebuilt Dashboard (styled table, status pills, row-action menu) and Create screen (5 cards, master-detail status editor) on top of it.
5. **Six correction pointers** from the user → used plan mode, wrote a plan, got approval, implemented:
   - Removed "+ Add Filter"
   - Create screen converted from a full-page swap to a **right-side drawer overlay at 70vw** (reused the reference's own `.backdrop`/`.drawer` components, just widened)
   - Care Setting Information panel made **dynamic** — selecting a Care Setting now rewrites the read-only info tags *and* the Facility dropdown options; empty-state message shown before any selection
   - **"Left Without Treatment" is not a status** — removed it from the status list; it's now a checkbox that appears in the status editor *only* when the status being edited is the Starting status, and follows the Starting flag if moved
   - Removed the generic "Other Exit Option" checkbox
   - **"+ Add Status" works anywhere** — insert points before/between/after every status row, fully functional (splices into the data array, re-renders)
   - Removed "Actions" from Patient Table → Common Columns (redundant with the separate Patient Actions checklist)
6. User shared a **screenshot of a different status-workflow configurator** and asked for analysis only, no code. Broke it into: patterns that confirmed our approach, patterns worth adopting, and ambiguous/flagged items not to act on without confirmation.
7. User said: adopt **only** the "worth adopting" list. One clarifying question asked (what a third row icon does) → answered: delete (✕). Implemented:
   - Replaced the old global flow-map diagram + "Allow backward transitions" checkbox with a **per-status "Allowed next statuses" checklist** (data model: `allowedNext` = array of status indices). Seeded to match the blueprint's own example, including the backward case Queued for Discharge → ED Admission.
   - Added **"Requirements before leaving this status"** — a new per-status checklist with an inline "add a requirement" input. Illustrative only, not real validation.
   - Restructured **Timer/Alert**: explicit "Enable timer" checkbox + separate Duration/Unit fields + Alert Message; fields disable when timer is off.
   - Status row actions are now **↑ / ↓ / ✕** (reorder + delete with confirm dialog; delete cleans up any `allowedNext` references pointing at the removed status).
   - Added helper text under Status Name ("Display name only. Existing transitions remain connected when renamed.") plus a required asterisk; editor panel header now shows the live status name + "Internal identifiers are hidden"; list panel header shows a live status count.
   - Added an inline scope-disclaimer note at the bottom of the Statuses & Workflow card, reworded for our actual structure: *"Widgets, Patient Table columns, and Patient Actions are configured in their own sections of this form — not duplicated here."*

## Current state of `Trackerboard_HighFidelity_Mockup.html`

- Single self-contained file (inline CSS + JS, no dependencies).
- CSS is in two `<style>` blocks: the **first is the copied reference design system — treat as read-only**; the second is mockup-specific additions (drawer width override, `.check-grid.cols-3`, etc.).
- **Dashboard**: table (Board Name, Care Setting, Facility, Updated Date, Status pill, row-action ⋮ menu with Edit/Duplicate/Delete/History Log). Facility/Care Setting filter selects only, no "Add Filter."
- **Create New Trackerboard**: right-side drawer, 70vw wide, dark backdrop dims the dashboard behind it. Quick-jump nav + 5 cards (Basic Information, Care Setting, Statuses & Workflow, Widgets, Patient Table). Footer: Cancel / Save as Draft / Activate.
- **Statuses & Workflow** is the most functionally built-out section — 3 seed statuses (Waiting List [Starting], ED Admission, Queued for Discharge [Completion]), each with: name/description, Starting/Completion radios, conditional Left-Without-Treatment toggle (Starting status only), Allowed-next-statuses checklist, Requirements checklist (with add-new), Timer/Alert block. List supports reorder (↑/↓), delete (✕, with confirm), and insert-anywhere ("+").
- Care Setting dropdown dynamically updates the Facility dropdown and the read-only info tags.

## Known gaps — flagged but intentionally not done

- No visual transition-graph/diagram anymore (removed in favor of the per-status checklist); nobody asked for a visual graph replacement.
- From the reference screenshot, **not adopted** (flagged as ambiguous, needs a decision if revisited): the screenshot showed Starting as a radio and Completion as a checkbox, which might imply multiple Completion statuses are allowed — that would conflict with the blueprint's "only one Completion Status" rule. Also didn't adopt its "Board Record" terminology (kept "Patient").
- Requirements checklist, most form fields, and Save/Activate buttons are visual only — no persistence, no real validation, no backend. This is a mockup, not a working prototype.
- Care-setting-specific Patient Table columns (OR/ED/IP tags) are static checkboxes with labels — not actually auto-filtered based on the Care Setting picked in section 2 (only the Facility dropdown + info tags are wired that way so far).
- The three V2.2 files in the same folder are unmerged leftovers from an earlier, different design direction — still present, not cleaned up.

## Environment quirk worth knowing

This session has a **display-only text corruption artifact**: when file contents are shown back through Read/Bash/Grep tool results, random substrings get mangled into placeholder tags (e.g. `<PERSON>`, `<LOCATION>`, `<CLINICAL_...>`). This does **not** affect real file bytes on disk — only what gets rendered back into the conversation. Confirmed by extracting the same content via shell redirection and grepping for zero occurrences of the artifact tags.

**Practical rule for future edits**: never hand-retype content that came from a Read/Bash/Grep result if it needs to be byte-exact (e.g. re-syncing the reference CSS block). Instead, extract/copy at the shell level (`sed`/`awk`/`cat` with `>` redirection) so bytes move disk-to-disk without passing through the corrupting display layer, and verify success only via `wc -l/-c` or `grep -c` counts — never by viewing the extracted content directly.

## Likely next steps (not yet requested, just visible from the gaps above)

- Decide whether to wire Care-Setting-specific Patient Table columns to actually react to the Care Setting selection (same pattern already used for Facility/info tags).
- Decide what to do with the leftover V2.2 files (archive/delete/keep for reference).
- Resolve the Starting-radio-vs-Completion-checkbox question if multi-completion-status support is ever wanted (currently: blueprint says only one Completion status, and the mockup enforces that via radio behavior).
