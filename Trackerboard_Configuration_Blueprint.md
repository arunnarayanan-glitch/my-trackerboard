# Trackerboard Configuration Blueprint

## Overview

The Trackerboard Configuration module will have two main screens:

1. **Trackerboard Dashboard**
2. **Create New Trackerboard**

---

# 1. Trackerboard Dashboard

The Trackerboard Dashboard is the main screen where users can view and manage the Trackerboards available to them.

What a user can see depends on their access permissions.

- A normal user may see Trackerboards created by them or assigned to them.
- An admin may also see Trackerboards created by other users.
- Access should follow the existing role and permission rules.

## 1.1 Dashboard Capabilities

The dashboard should allow users to:

- View the list of available Trackerboards.
- Search for a Trackerboard.
- Filter Trackerboards by:
  - Facility
  - Care Setting
  - Other filter categories that may be added later.
- Create a new Trackerboard.
- Manage existing Trackerboards.

## 1.2 Trackerboard List

Each Trackerboard in the list or table should show the following information:

| Field | Example |
|---|---|
| Board Name | Emergency Department Trackerboard |
| Care Setting | Emergency Department |
| Facility | Central City Health |
| Updated Date | 07 Aug 2026 |
| Status | Active, Draft, Inactive |
| Actions | Edit, Duplicate, Delete, History Log |

### Available Actions

Users may be able to:

- **Edit** the Trackerboard.
- **Duplicate** the Trackerboard.
- **Delete** the Trackerboard.
- **View History Log** to see previous changes.

Available actions should depend on the user's permissions.

---

# 2. Create New Trackerboard

The Create New Trackerboard screen allows the user to configure all the main parts required for a Trackerboard.

The configuration is divided into the following areas:

1. Basic Information
2. Care Setting
3. Statuses & Workflow
4. Widgets
5. Patient Table

---

# 2.1 Basic Information

This section contains the basic details of the Trackerboard.

| Field | Example |
|---|---|
| Name | Emergency Department Trackerboard |
| Short Name | EDT |
| Unique ID | EDT001 |
| Description | A short description of the Trackerboard |

### Fields

#### Name
The full name of the Trackerboard.

Example:

`Emergency Department Trackerboard`

#### Short Name
A shorter name that can be used in places where space is limited.

Example:

`EDT`

#### Unique ID
A unique system identifier for the Trackerboard.

Example:

`EDT001`

#### Description
A short explanation of what the Trackerboard is used for.

---

# 2.2 Care Setting

This section connects the Trackerboard to a Care Setting and Facility.

## Care Setting Selection

The user selects the Care Setting that the Trackerboard is designed for.

Example:

`Emergency Department`

## Facility Selection

After selecting the Care Setting, the user can select a Facility that is already associated with that Care Setting.

Example:

`Central City Health`

The Facility list should come from the selected Care Setting configuration.

## Care Setting Information

After selecting the Care Setting, the system should display important information about it as read-only information.

This may include:

- Default Visit Category
- Expected Length of Stay
- Chief Complaint Form
- Other Care Setting configuration details

These values are configured inside **Care Setting Configuration**, which is outside the scope of this Trackerboard Configuration.

---

# 2.3 Statuses & Workflow

Statuses and Workflow define how patients move through the Trackerboard.

This is one of the most important parts of the configuration.

Each Trackerboard can contain multiple statuses.

Examples:

- Waiting List
- ED Admission
- Pre-OR
- In Procedure
- Recovery
- Queued for Discharge
- Discharged

## Status Configuration

Each status should contain the following:

### Name

The name of the status.

Examples:

- Waiting List
- Pre-OR
- ED Admission

### Description

A short explanation of what the status represents.

### Starting Status

One status can be marked as the **Starting Status**.

This represents where a patient normally enters the Trackerboard workflow.

Only **one Starting Status** can exist within a Trackerboard.

### Completion Status

One status can be marked as the **Completion Status**.

This represents the normal end of the Trackerboard workflow.

Only **one Completion Status** can exist within a Trackerboard.

### Other Exit Options

Some workflows may also require exit options that are not normal completion statuses.

For example:

- Left Without Treatment

This type of outcome should be supported without creating multiple Starting or Completion statuses.

## Status Transitions

The configuration should define how a patient can move between statuses.

Example:

`Waiting List → ED Admission → Queued for Discharge`

The workflow may also allow a patient to move backwards when required.

Example:

`Queued for Discharge → ED Admission`

This means transitions should not always be one-directional.

## Transfer to Another Care Setting

The Trackerboard should support transferring a patient from one Care Setting to another.

Example:

`Emergency Department → Operating Room → Inpatient`

The transfer rules should define which destination Care Settings are allowed.

## Status Alert / Timer

A status can have a timer or alert rule.

This helps identify patients who are staying in a status longer than expected.

Example:

If a patient remains in **Waiting List** for more than the configured time, the Trackerboard can show an alert.

---

# 2.4 Widgets

Widgets provide a quick overview of the current Trackerboard situation.

Different Care Settings may require different widgets.

Common widgets in Emergency Department, Inpatient, and Operating Room Trackerboards include the following.

## Total Patients

Shows the total number of patients currently on the Trackerboard.

The widget may also show additional breakdowns depending on the Care Setting.

### Emergency Department Example

Patients may be grouped by triage level:

- High Risk
- Medium Risk
- Low Risk

### Inpatient Example

Patients may be grouped by unit or sub-area:

- ICU
- General Hospitalization
- Other Inpatient Units

### Operating Room Example

Patients may be grouped by workflow status:

- Pre-OR
- In OR
- Post-OR

## Resource Availability

Shows available resources compared with the total number of resources.

Examples:

- Beds
- Rooms
- Chairs

Example:

`12 Beds Available / 30 Total`

## Code Team

Shows the members of the Code Team.

The widget may display:

- Name
- Role

## Doctors on Duty

Shows the doctors who are currently on duty.

The widget may display:

- Doctor Name
- Role or Specialty

## Cardex Panel

The Cardex panel usually appears as a side panel and shows important clinical alerts and pending items.

Possible sections include:

- Awaiting Approvals
- MAR
- Medications
- Lab / Radiology
- Rejected Medication List

---

# 2.5 Patient Table

The Patient Table is the main working area of the Trackerboard.

It displays the list of patients and the information required by the care team.

The available columns may vary depending on the Care Setting.

## Common Patient Columns

Common columns may include:

- Patient Name
- PID
- Age
- Assigned Bed
- Vitals
- Care Setting
- Actions

## Bed Assignment

The table should show the patient's assigned bed number.

If the patient does not have a bed, the user may be able to assign one directly from the Trackerboard.

## Vitals

The table may show recent vital information.

Users may also have an option to add new vitals.

## Patient Actions

The Trackerboard may provide patient-level actions such as:

- Status Change
- Encounter Note
- Referral
- Intake Form
- Documents
- Cardex Alerts
- Payment
- Past Visits
- Specimen Collection
- Patient Demographics

The available actions may depend on the user's role and permissions.

---

# 2.6 Care Setting-Specific Columns

Some columns should appear only for certain Care Settings.

## Operating Room

Possible columns:

- Scheduled Time
- Surgery Type

## Emergency Department

Possible columns:

- Chief Complaint
- Triage Level

## Inpatient

Possible columns:

- Diagnosis

Additional Care Setting-specific columns may be added later.

---

# 2.7 Expanded Patient Row

A patient row can be expanded to show additional information and quick actions.

Possible fields include:

## Provider

Select or assign the patient's provider.

## Nurse

Select or assign the patient's nurse.

## Patient Type

Select the patient's type or classification.

## Medical Conditions

The user may record important medical conditions.

Examples may include:

- HIV / AIDS
- Hepatitis B
- MRSA
- Dengue Fever
- Other relevant conditions

## Isolation Information

If isolation is required, the user may record the reason for isolation.

## Allergies

The user may add or review patient allergies.

---

# Summary

The Trackerboard Configuration should allow administrators or authorized users to define how a Trackerboard works without changing the underlying Care Setting configuration.

The main configuration areas are:

- Basic Trackerboard information
- Care Setting and Facility
- Patient workflow and statuses
- Transfers between Care Settings
- Status timers and alerts
- Dashboard widgets
- Patient table columns
- Patient-level actions
- Care Setting-specific information
- Expanded patient details

The goal is to make Trackerboards configurable enough to support different clinical workflows while still following access controls and the rules defined by the selected Care Setting.
