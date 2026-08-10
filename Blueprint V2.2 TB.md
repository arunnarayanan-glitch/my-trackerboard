# Trackerboard Configuration — Design Blueprint

**Status:** Draft
**Date:** August 6, 2026
**Related:** Solution V2.2 TB.md
**Purpose:** Define the screens, flow, and layout for the Trackerboard configuration module before wireframing begins.

---

## 1. Screen Inventory

1. **Trackerboard List** — all boards, status (Draft / Active / Inactive), entry point to create a new board.
2. **New Trackerboard Entry** — choice of "Start from Existing Template" or "Start from Blank Board."
3. **Board Editor** — opens as a slide-over drawer on top of the Trackerboard List (List stays visible/dimmed behind), not a separate full page (see Section 2).
4. **Version History / Activation** — reached from the List screen, outside the editor.

---

## 2. Board Editor — Section Breakdown

The editor is a single continuous form, divided into 5 sections stacked top to bottom. A quick-jump nav at the top of the drawer scrolls directly to any section. Each section is a flat form or a set of accordions grouping related components.

Changed from an earlier paginated-wizard decision: board setup is a one-time procedure in most cases, and later edits target one specific section rather than the whole form — pagination added friction without a matching benefit.

| Section | Content |
|---|---|
| **1. Basic Details** | Board Details & Scope (name, Care Setting, Facility, description, active state), Header Controls (Board name, Facility, Change date, Add patient, Search patient, Refresh) |
| **2. Widgets & Panels** | Accordions: Patients, Resources, Code Team, Doctors on Duty, Cardex Panel (MAR, Medications, Lab/Radiology, Rejected Medication List) |
| **3. Patient Table** | Accordions: Columns, Actions |
| **4. Rules & Access** | Status & Workflow, Permissions (config-time and runtime view/manage mapping) |
| **5. Review & Save** | Summary of all configured sections; Save as Draft or Activate |

**Re-edit access:** the quick-jump nav is available at all times — admins jump straight to the section they need to change without scrolling past the rest of the form.

**Accordion fields:** all fields (enabled, title, order, position, size, permissions, click/edit behavior, empty/error state, etc.) display inline within each accordion for now. This can move to a sub-form or side panel later if accordions get too tall.

---

## 3. Layout Decisions

- **Component placement:** fixed ordered slots for widgets and table columns — no free-form drag-and-drop canvas.
- **Editor container:** slide-over drawer, not a separate full page.
- **Editor navigation:** single continuous scrolling form with a quick-jump nav, not a paginated wizard or a tab-only workspace. Accordion grouping is still used within the Widgets & Panels and Patient Table sections.
- **Lifecycle for this pass:** Draft → Active → Inactive. Preview (from the solution doc's §15 lifecycle) is deferred — not designed in this pass.

---

## 4. Scope

**Covers**

- Visibility, order, labels, permissions, and position/size of existing components.
- Both flows: rebuilding the existing IP/ED boards to parity, and creating a new board from a template or blank.

**Does not cover**

- New component types or custom action logic.
- Editing master data owned elsewhere (roles, shifts, statuses master lists, resource calculation, etc.).
- A generic, administrator-defined roster builder.
- Free-form drag-and-drop layout.
- The Preview screen.

---

## 5. Design Approach

Build **Section 2 (Widgets & Panels)** first, in high fidelity — it has the most complex pattern (multiple accordions, each with ~8 config fields, two of which — Code Team and Doctors on Duty — carry additional roster-management behavior). Validate the interaction pattern on this section, then reuse it to move quickly through Sections 1, 3, and 4.

---

## 6. Deferred / Open

- Preview screen design.
- Board name uniqueness rule (system-wide vs. per Care Setting).
- Cardex role-visibility scope (per-widget vs. per-panel).
- Roles: global list vs. per-Care-Setting.
- Ownership/process for the readiness checks described in Solution §8.6.
- Whether inline accordion fields remain workable at full field count, or need to move to a sub-form/side panel.
