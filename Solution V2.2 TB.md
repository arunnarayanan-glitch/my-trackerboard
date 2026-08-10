# Solution V2.2 — Trackerboard Configuration

**Status:** Draft / Headstart  
**Date:** August 6, 2026  
**Revision:** Corrected using the documented IP Code Team and ED Doctors on Duty behavior  
**Focus:** Rebuild the existing Trackerboard through configuration, then use the same configuration model to create new Trackerboards.

---

## 1. Purpose

This solution defines how the current Trackerboard can be represented as a reusable configuration instead of being built as a fixed screen.

The first goal is **feature parity**: every component and action that already works in the existing Trackerboard must be available in the configuration model.

Only after the existing board has been rebuilt and verified should new features be added.

---

## 2. Problem

The existing Trackerboard already contains important operational features, widgets, patient information, and actions.

If the new configuration supports only a simplified board, it may remove or weaken functions that users already depend on. It may also force the product team to write custom code each time a new Trackerboard is needed.

We therefore need a configuration model that can:

1. Recreate the current Trackerboard without losing any existing function.
2. Reuse the same components for new Care Settings and new Trackerboards.
3. Keep board-specific choices separate, so changing one board does not affect another board.
4. Protect existing permissions, clinical rules, and data sources.

---

## 3. Proposed Solution

Create a **component-based Trackerboard configuration**.

Each Trackerboard will be stored as a configuration that defines:

- Board identity and Care Setting context
- Header controls
- Summary widgets
- Right-panel Cardex widgets
- Patient table columns
- Patient-level actions
- Status and workflow behavior required by those actions
- Permissions and visibility rules
- Component order and placement
- Runtime entry points for operational functions such as Code Team and Doctors on Duty roster management

The configuration will not recreate the underlying clinical functions. It will connect to and reuse the functions that already exist.

For example, enabling **Encounter Note** in a board configuration should open the existing Encounter Note function. The configuration should not create a second Encounter Note implementation.

---

## 4. Solution Approach

This solution will follow a **baseline-first approach**.

### Stage 1 — Existing Board Baseline

Capture the existing Trackerboard exactly as it works today, including:

- Components shown
- Component order
- Labels
- Data displayed
- Data sources
- Permissions
- Availability conditions
- Click and edit behavior
- Runtime add or management flows
- Required fields and selection rules
- Confirmation dialogs and informational warnings
- Empty states
- Error states

### Stage 2 — Rebuild Through Configuration

Create an **Existing Trackerboard Template** using only the new configuration model.

The configured board must be compared with the current board until the behavior matches.

### Stage 3 — Create New Trackerboards

After parity is confirmed, teams can create new boards by:

- Starting from the existing template, or
- Starting from a clean board

All existing components must remain available in the component library, even when a new board does not need to display every component.

### Stage 4 — Add New Features

New widgets, actions, layouts, automation, and workflow options can be added only after the baseline configuration is complete and accepted.

---

## 5. Configuration Model

The Trackerboard configuration will be divided into the following areas.

```text
Trackerboard Configuration
├── Board Details and Scope
├── Header Controls
├── Main Widgets
│   ├── Summary Widgets
│   └── Operational Roster Widgets
├── Cardex / Approval Panel
├── Patient Table
│   ├── Columns
│   └── Actions
├── Status and Workflow Dependencies
├── Permissions and Conditions
└── Version and Activation
```

---

## 6. Board Details and Scope

### 6.1 Board Name

The administrator can define the name displayed on the Trackerboard.

Minimum settings:

- Board name
- Short name, when required by the existing UI
- Description for administrators
- Active or inactive state

### 6.2 Care Setting

The Trackerboard must be connected to its applicable Care Setting context.

The Care Setting should remain the source of truth for clinical context and related mappings.

### 6.3 Facility

Facility information must continue to appear and work on the Trackerboard.

However, the Trackerboard configuration should not create a separate, conflicting Facility mapping when the selected Care Setting already owns that relationship.

Recommended behavior:

- Applicable Facilities are inherited from the selected Care Setting.
- The runtime board can show the Facility or allow the user to switch between applicable Facilities, based on the existing behavior.
- The configuration can control whether the Facility control is visible and where it appears.
- The configuration cannot attach a Facility that is not valid for the selected Care Setting.

Confirmed: a Trackerboard maps to a single Care Setting. A single Facility (e.g., a hospital or clinic) can have multiple Care Settings, each with its own Trackerboard.

---

## 7. Header Controls

The following existing controls must be available in the configuration.

| Existing control | Configuration support | Expected baseline behavior |
|---|---|---|
| **Board name** | Text and display position | Shows the configured board name. |
| **Facility** | Visibility and applicable Facility context | Uses Facility mappings inherited from the Care Setting. |
| **Change date** | Enable/disable, label, position, default date | Defaults to the current date, shown with a **Today** badge. Allows the user to view the board for a selected date using the existing rules. |
| **Add new patient** | Enable/disable, position, permission, availability condition | Starts the existing add-patient flow. |
| **Search patient to add** | Search rules, permission, result behavior | Uses the existing patient search and selection flow. |
| **Refresh** | Enable/disable and position | Refreshes the board using the existing refresh behavior. |

For each control, the configuration should support:

- Visible or hidden
- Display order
- Label, where changing the label is safe
- Required permission
- Availability condition
- Existing destination or function

The configuration must not bypass validation that already exists in the Add Patient or Patient Search flows.

---

## 8. Main Widgets

All existing widgets must be available in the Trackerboard component library. This does not mean every widget must be enabled on every board. Existing board-specific defaults must be preserved—for example, the supplied workflow places Code Team on the IP Trackerboard and Doctors on Duty on the ED Trackerboard.

Each widget should support a common set of configuration options:

- Enabled or disabled
- Widget title
- Display order
- Position
- Size, within supported layout rules
- Applicable Care Setting or board context
- View permission
- Manage permission, when the widget supports runtime updates
- Click or edit behavior
- Empty state
- Error state

Some widgets only display information. **Code Team** and **Doctors on Duty** also allow authorized users to update operational roster data from the active Trackerboard.

The configuration may control component availability, layout, and permissions. The component must automatically use its approved data source, fields, validation, confirmation, and save behavior. A board administrator must not be allowed to connect it to an unrelated source or redefine its runtime form in V2.2.

### 8.1 Widget 1 — Patients

The Patients widget must support:

- Total number of patients
- Triage breakdown by High/Medium/Low risk, where applicable
- Click-through behavior, when supported today

**Sorted by triage** means a breakdown by High/Medium/Low risk. This is an ED-specific behavior and is optional per board, not mandatory on every Trackerboard.

### 8.2 Widget 2 — Resources

The Resources widget must support the existing resource types:

- Available beds
- Available chairs
- Available lobby spaces

The displayed resource types should come from the resources configured for the selected Care Setting and Facility.

The widget may allow the administrator to choose which supported resource types are shown, but it must not calculate availability separately from the existing resource-management logic.

### 8.3 Widget 3 — Code Team

The Code Team component is an **interactive operational roster widget** currently used on the IP Trackerboard. Rebuilding the widget means preserving both its display and its add-member flow.

#### Confirmed existing behavior

1. An authorized user selects the **Edit** icon next to the Code Team section.
2. The **Code Team** slide-in panel opens.
3. The panel shows the existing team and an **Add New** action.
4. Selecting **Add New** displays the add-member section.
5. In **Name**, the user searches for and selects one user.
6. In **Role**, the user selects a designation or role value.
7. In **Shift**, the user selects a shift slot.
8. Selecting **Add** opens a confirmation dialog.
9. Selecting **Yes** adds the member to the Code Team.

The existing Code Team list contains these items:

| Item | Existing behavior |
|---|---|
| **Name** | Shows the member's name. The supplied documentation says the designation is also shown with the name. |
| **Role** | Shows the selected Code Team role or designation. |
| **Shift** | Shows the assigned shift. |
| **Add New** | Opens the add-member section. |

When data is entered for the current shift, the existing informational message appears:

> Previous shift data will be removed.

This message does not require another confirmation. The exact trigger and the data removed are deferred for confirmation in a later phase and do not block the baseline rebuild.

The supplied note about **Inactive features** is unrelated to Code Team and is excluded from this solution.

#### What the board configuration controls

- Whether the Code Team component is enabled when the selected context supports it
- Widget title, position, order, and supported size
- Who can view the component
- Who can open the existing Add flow

#### What remains fixed in the existing component

- Supported board and Care Setting contexts
- Automatic display of the Edit entry point when the user has the existing roster permission
- User search source
- Single-user selection for each Add action
- Name, Role, and Shift fields
- Role or designation values
- Shift values
- Mandatory fields: Name and Shift. Role is not mandatory.
- Add confirmation behavior
- Current-shift informational message and replacement behavior
- Existing validation and audit behavior, if currently implemented
- Roster storage and save behavior

The active Code Team roster is runtime operational data. It must not be stored as a fixed list of members inside the Trackerboard configuration.

### 8.4 Widget 4 — Doctors on Duty

The Doctors on Duty component is an **interactive provider roster widget** currently used on the ED Trackerboard. It is not only a read-only provider schedule.

#### Confirmed existing behavior

1. An authorized user selects the **Edit** icon next to the Doctors on Duty section.
2. The **Doctors on Duty** slide-in panel opens.
3. The panel provides an **Add New** action.
4. Selecting **Add New** displays the add-provider section.
5. The user searches for and selects one or more providers.
6. The user selects a shift slot.
7. Shift selection is mandatory.
8. Selecting **Add** opens a confirmation message.
9. Selecting **Yes** adds the selected provider or providers to the Doctors on Duty list.

Entries are organized in tables grouped by shift. For example, a **Morning Shift** table lists each provider with:

| Item | Existing behavior |
|---|---|
| **Name** | Shows the provider's name. |
| **Phone** | Shows the provider's phone number. |
| **Email** | Shows the provider's email address. |

Existing entries can be deleted. There is no edit action — a change requires deleting the entry and re-adding the provider with the correct details.

#### What the board configuration controls

- Whether the Doctors on Duty component is enabled when the selected context supports it
- Widget title, position, order, and supported size
- Who can view the component
- Who can open the existing Add flow

#### What remains fixed in the existing component

- Supported board and Care Setting contexts
- Automatic display of the Edit entry point when the user has the existing roster permission
- Provider search source and provider eligibility rules
- Selection of one or more providers
- Shift values
- Mandatory Shift validation
- Add confirmation behavior
- Existing duplicate or shift-conflict behavior, if currently implemented
- Existing audit behavior, if currently implemented
- Roster storage and save behavior
- Shift-grouped display with Name, Phone, and Email columns
- Delete-only entry management; no edit action

The active Doctors on Duty roster is runtime operational data. It must not be stored as a fixed provider list inside the Trackerboard configuration.

### 8.5 Shared Framework, Separate Component Rules

Code Team and Doctors on Duty can reuse a common roster-widget shell, such as the Edit entry point, slide-in layout, permission handling, Add action, confirmation pattern, and empty/error states. They must still remain separate component types in the parity phase because their field and selection rules are different.

| Rule | Code Team | Doctors on Duty |
|---|---|---|
| Existing board | IP Trackerboard | ED Trackerboard |
| Search entity | User | Provider |
| Selection per Add action | One user | One or more providers |
| Role/designation field | Present; not mandatory | Not identified in the supplied flow |
| Shift | Selected; mandatory | Mandatory |
| Confirmation | Add, then Yes | Add, then Yes |
| Special message | Previous-shift removal message | No equivalent message identified |

V2.2 must preserve these differences. A fully administrator-defined generic roster form would be a future enhancement, not part of the existing-board rebuild.

### 8.6 Readiness for Use on a New Trackerboard

Making these components available in the library does not mean they can be activated in every board without validation.

Before a new Trackerboard can use Code Team or Doctors on Duty, the system must confirm that:

- The component supports the selected board and Care Setting context.
- The required user or provider search service is available.
- The role or designation source is available when Code Team is used.
- The shift source is available.
- The runtime roster can resolve its Facility, Care Setting, board, date, and shift context using the existing rules.
- View and manage permissions are mapped.
- The existing add, validation, confirmation, and save flow can run without custom board-specific code.

If a required dependency is missing, activation should be blocked with a clear configuration issue rather than allowing a partially working widget.

---

## 9. Cardex / Awaiting Approvals Panel

The existing right-side Cardex area must be available in the configuration.

The baseline panel includes:

- MAR
- Medications
- Lab / Radiology
- Rejected Medication List

The configuration should support:

- Show or hide the complete panel
- Show or hide each supported Cardex widget
- Widget order
- Widget title
- Role visibility
- Count or summary display
- Existing click-through destination
- Empty and error states

The meaning of each approval count and the conditions that place an item in **Awaiting Approvals** must remain controlled by the existing Cardex, MAR, medication, laboratory, and radiology rules.

The Trackerboard configuration must not create a separate approval engine.

---

## 10. Patient Table Configuration

The Patient Table must be built from reusable column definitions.

For every supported column, the configuration should define:

- Visible or hidden
- Display order
- Column label
- Width within supported limits
- Alignment and format
- Sort availability
- Filter availability
- Role visibility, when needed
- Existing data source
- Empty-value behavior

The configuration may control presentation, but it must not change the clinical meaning of a field.

### 10.1 Required Existing Columns

| Existing column | Required configuration behavior |
|---|---|
| **Name / PID** | Displays patient identity using the existing privacy and formatting rules. This should remain a protected core column. |
| **Care Setting** | Displays the patient's current Care Setting from the existing encounter or patient-flow source. |
| **Chief Complaint** | Displays the recorded chief complaint using the current source and formatting. |
| **Vitals** | Displays the existing vitals summary or indicator. The exact values and recency rules must be inherited from the current board. |
| **Triage** *(ED only; optional elsewhere)* | Displays the patient's current triage level and preserves the existing priority meaning. |
| **Time Spent** | Uses the existing start point and calculation. The exact start event must be documented during inventory. |
| **Bed No.** | Displays the assigned resource and preserves the existing option to assign or change a bed when permitted. |
| **Actions** | Displays the configured patient actions while preserving permissions and availability rules. |

### 10.2 Bed Assignment

The **Bed No.** column must continue to support **Assign new bed**, when the user has permission and a valid resource is available.

The configuration can control whether the assignment control is shown on a board. It cannot bypass:

- Resource availability rules
- Facility and Care Setting compatibility
- Double-booking prevention
- Existing transfer or assignment rules
- Permission checks

---

## 11. Patient Actions

All existing patient actions must be available in the configuration library.

For each action, the configuration should support:

- Enabled or disabled
- Display order
- Label and icon from the approved design system
- Required permission
- Patient-status conditions
- Care Setting conditions
- Existing destination or workflow
- Confirmation behavior, when already required
- Disabled-state reason

V2.2 should reuse the existing action implementations. It should not support administrators creating arbitrary new clinical actions or entering custom executable logic.

### 11.1 Required Existing Actions

| Existing action | Configuration responsibility |
|---|---|
| **Status Change** | Show the action and connect it to the board's configured statuses and allowed transitions. |
| **Encounter Note** | Open the existing Encounter Note function for the correct patient and encounter. |
| **Referral** | Open the existing referral flow and preserve its current validation. |
| **Intake Form** | Open the existing intake flow and preserve appointment and patient conditions. |
| **Documents** | Open the existing patient or encounter document area. |
| **Cardex Alerts** | Open the existing Cardex alert details for the selected patient. |
| **Payment** | Open the existing payment flow and preserve financial permissions. |
| **Past Visits** | Show the patient's existing visit history. |
| **Specimen Collection** | Open the existing specimen-collection workflow when applicable. |
| **After Visit Summary** | Open or generate the existing After Visit Summary when the required conditions are met. |
| **Patient Demographics** | Navigate to the existing patient demographic record using the user's current access rights. |

Hiding an action in the interface is not a replacement for backend authorization. Existing permission checks must still run whenever an action is opened or performed.

---

## 12. Status and Workflow Dependencies

Some visible components depend on board workflow configuration, especially **Status Change**, patient counts, triage sorting, time spent, and action availability.

Confirmed: each Trackerboard defines its own status set and transitions independently, based on that board's requirements. Status names and rules are not shared from a single global master list across boards.

The minimum workflow support required for parity is:

- Existing status names
- Status order
- Start and completion statuses
- Allowed status transitions
- Existing restrictions on skipping or reversing a status
- Conditions that enable or disable patient actions
- Existing timestamps used by the board
- Existing audit behavior

The first configured template should copy the current board's workflow rules. V2.2 should not introduce a redesigned workflow while parity is being established.

---

## 13. What Is Configurable and What Remains Controlled

### 13.1 Board-Level Configuration

The administrator may configure supported presentation and board choices such as:

- Board name
- Component visibility
- Component order and supported placement
- Widget titles
- Patient table column visibility and order
- Patient action visibility and order
- Applicable roles
- Supported status and transition setup
- Care Setting context
- Whether Code Team and Doctors on Duty are available on the board
- Which roles can view or manage each roster widget

The board configuration may enable an approved roster component and assign view or manage permissions. Its user, provider, role, and shift bindings remain controlled by the product component. The board definition must not store the live daily roster.

### 13.2 Centrally Controlled Behavior

The following should not be freely remapped by a board administrator:

- Patient identity rules
- Clinical meaning of data
- Source systems and approved APIs
- Triage meaning
- Vitals calculation and recency rules
- Resource availability calculation
- Medication, MAR, laboratory, and radiology approval logic
- User and provider directory records
- Code Team role or designation master data
- Shift definitions and scheduling rules
- Live Code Team and Doctors on Duty assignments
- Billing and payment rules
- Backend permissions
- Audit requirements
- Safety validation

This separation makes the solution configurable without allowing one board to redefine core product behavior or overwrite daily operational data.

---

## 14. Permissions

Two permission levels are required.

### 14.1 Configuration Permissions

Controls who can:

- Create a board
- Edit a draft board
- Change layout and components
- Change statuses and transitions
- Define which runtime roles can view or manage roster widgets
- Activate a board
- Deactivate a board

Configuration permission does not automatically grant permission to manage the live Code Team or Doctors on Duty roster.

### 14.2 Runtime Permissions

Controls what a user can view or do inside the active Trackerboard.

Runtime permissions must continue to use the existing permissions for actions such as:

- Add patient
- Change status
- Assign a bed
- Write an Encounter Note
- Create a referral
- View documents
- Take payment
- View demographics
- View Code Team
- Add Code Team members through the existing flow
- View Doctors on Duty
- Add Doctors on Duty providers through the existing flow
- Edit or remove roster entries only when those existing functions are confirmed and permitted

A board configuration may hide an action or widget from a role, but it must never grant access that the underlying module or roster service does not allow.

---

## 15. Configuration Lifecycle

A simple lifecycle is recommended:

1. **Draft** — The board is being configured and is not available to operational users.
2. **Preview** — An administrator can review the board using test or controlled data.
3. **Active** — The board is available to authorized users.
4. **Inactive** — The board is no longer used, but its configuration history is retained.

Configuration changes should be saved as a new version so that:

- An active board is not changed accidentally.
- Previous settings can be reviewed.
- Changes can be traced to a user and time.
- A failed change can be rolled back safely.

This is a platform control for safe configuration, not a new clinical Trackerboard feature.

---