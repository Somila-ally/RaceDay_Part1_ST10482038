# RaceDay — Part 1: System Planning & Database

## 1. System Description

RaceDay is a race-event management system for organising, planning and
administering running events (such as a Marathon, Fun Run or Trail event).
Each event can be split into one or more categories (such as 5 km, 10 km or
Half Marathon). Participants register for a category of their choice, and
results can be viewed once the organiser has recorded them on race day.

The system is designed to be REST API-based, with a SQL Server database for
data storage, to be constructed during Part 2 of the POE.

## 2. Roles

| Role | Description |
|---|---|
| Organiser | Manages and creates Events and Event Categories, views enrolments by Category, and captures/corrects Event Results after the event. |
| Participant | Accesses events, registers for one or more categories, maintains own profile, and views own enrolments and results. |

Both roles are `Users`, distinguished by `RoleId`. Any user, regardless of
role, registers, logs in and manages their profile in the same way.

## 3. Repository Structure

```
.
├── docs/
│   ├── ERD.pdf                 # Entity Relationship Diagram (all 6 entities)
│   ├── API_Endpoint_Plan.md    # Full REST API endpoint plan
│   └── database.sql            # T-SQL script: schema + seed data
├── .github/
│   └── workflows/
│       └── validate.yml        # CI check that the planning artifacts exist
├── .gitignore
└── README.md
```

## 4. Entity Relationship Diagram

See `docs/ERD.pdf` for the full diagram, covering all six entities — Roles,
Users, Events, Categories, Enrolments and Results — with all attributes,
primary keys, foreign keys and cardinalities.

Summary of relationships:

- Roles (1) — (Many) Users
- Users/Organiser (1) — (Many) Events
- Events (1) — (Many) Categories
- Users/Participant (Many) — (Many) Categories, via the `Enrolments` junction table
- Enrolments (1) — (1) Results


## 5. API Endpoint Plan

See `docs/API_Endpoint_Plan.md` for the complete set of endpoints, covering
Authentication, User Profile, Events, Categories, Event Enrolments and
Results.

---
