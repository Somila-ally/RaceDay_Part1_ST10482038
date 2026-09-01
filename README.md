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

## 6. SQL Database Script

`docs/database.sql` is a single T-SQL script for SQL Server Management Studio
(SSMS). It creates every table in the ERD with all primary keys, foreign
keys and constraints, then seeds:

- 2 Organisers and 2 Participants
- 3 Events
- Categories for each event
- Sample enrolments and one sample result

It matches the ERD exactly and runs cleanly on a fresh SQL Server instance,
from top to bottom.

## 7. GitHub & CI/CD

All planning docs and the SQL script are committed under `/docs`. The
GitHub Actions workflow at `.github/workflows/validate.yml` runs on every
push/PR to `main` and confirms:

- The `/docs` folder exists
- `ERD.pdf`, `API_Endpoint_Plan.md` and `database.sql` are present
- `README.md` exists and covers the required sections
- `database.sql` creates every required table

## 8. YouTube Demonstration

*Add the link to the Part 1 walkthrough video here before submission.*

---
## 9. Suggested Commit History (30 commits)

The brief requires 20+ meaningful commits on your own GitHub account. Rather
than pushing everything in one commit, work through the sequence below —
each step is a small, logical, real change, in an order that would make
sense if you were actually building this up. Commit after completing each
row.

| # | Commit Message | Files Touched |
|---|---|---|
| 1 | Initial commit: repository scaffold and .gitignore | `.gitignore` |
| 2 | Add README with system description | `README.md` |
| 3 | Add roles section to README | `README.md` |
| 4 | Create docs folder structure | `docs/` |
| 5 | Add ERD draft: entities and attributes only | `docs/ERD.pdf` |
| 6 | Add ERD relationships and cardinality | `docs/ERD.pdf` |
| 7 | Finalise and export ERD to PDF | `docs/ERD.pdf` |
| 8 | Add API_Endpoint_Plan.md skeleton with headings | `docs/API_Endpoint_Plan.md` |
| 9 | Add Authentication endpoints to API plan | `docs/API_Endpoint_Plan.md` |
| 10 | Add User Profile endpoints to API plan | `docs/API_Endpoint_Plan.md` |
| 11 | Add Events endpoints to API plan | `docs/API_Endpoint_Plan.md` |
| 12 | Add Categories endpoints to API plan | `docs/API_Endpoint_Plan.md` |
| 13 | Add Event Enrolments endpoints to API plan | `docs/API_Endpoint_Plan.md` |
| 14 | Add Results endpoints to API plan | `docs/API_Endpoint_Plan.md` |
| 15 | Add ERD relationship summary to API plan | `docs/API_Endpoint_Plan.md` |
| 16 | Add database.sql skeleton: database creation | `docs/database.sql` |
| 17 | Add Roles and Users table definitions | `docs/database.sql` |
| 18 | Add Events table definition | `docs/database.sql` |
