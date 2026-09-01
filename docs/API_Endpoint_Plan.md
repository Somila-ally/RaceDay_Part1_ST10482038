# RaceDay — API Endpoint Plan

**Module:** PROG6212 — Programming 2B — POE
**Part:** Part 1 — System Planning & Database
**Author:** Somila

---

## 1. Overview

RaceDay is a REST API-based race-event management system, backed by a SQL Server
database, used for organising, planning and administering running events (for
example a Marathon, Fun Run or Trail event). Each event can be split into one or
more categories (such as 5 km, 10 km or Half Marathon). Participants register for
a category of their choice, and results are recorded and viewed once the
organiser has captured them on race day.

All routes are prefixed with `/api/`.

**Roles:** `Organiser` and `Participant` — both are `Users`, distinguished by
`RoleId`.

- **None** — no authentication token required
- **Any** — any authenticated user
- **Organiser** — an authenticated user with the Organiser role (further
  restricted to the owner of the parent resource where noted)
- **Participant** — an authenticated user with the Participant role

---

## 2. Roles

| Role | Description |
|---|---|
| Organiser | Manages and creates Events and Event Categories, views enrolments by Category, and captures/corrects Event Results after the event. |
| Participant | Accesses events, registers for one or more categories, maintains own profile, and views own enrolments and results. |

---

## 3. Authentication (`/api/auth`)

| Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| POST | /api/auth/register | Registers a new user as either an Organiser or a Participant | None | `{ fullName, email, password, role }` | 201 Created with new user id; 400 Bad Request if validation fails; 409 Conflict if email already exists |
| POST | /api/auth/login | Authenticates a user and returns a JWT | None | `{ email, password }` | 200 OK with `{ token, role, userId }`; 401 Unauthorized if credentials are invalid |
| POST | /api/auth/logout | Invalidates the current session/token | Any | (none — token in header) | 200 OK; 401 Unauthorized if no valid token supplied |

---

## 4. User Profile (`/api/users`)

| Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/users/{id} | Retrieves a user's profile | Any (own) / Organiser (any) | (none) | 200 OK with profile object; 404 Not Found if id doesn't exist; 403 Forbidden if requesting another user's profile without rights |
| PUT | /api/users/{id} | Updates a user's profile details | Any (own profile only) | `{ fullName, email }` | 200 OK with updated profile; 400 Bad Request on invalid data; 403 Forbidden if not own profile |
| DELETE | /api/users/{id} | Deactivates/deletes a user account | Any (own account) | (none) | 204 No Content; 403 Forbidden if not own account; 404 Not Found if id doesn't exist |

---

## 5. Events (`/api/events`)

| Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/events | Lists all upcoming events (supports `?location=` and `?date=` filters) | None | (none) | 200 OK with array of events |
| GET | /api/events/{id} | Retrieves a single event's detail, including its categories | None | (none) | 200 OK with event object; 404 Not Found if id doesn't exist |
| POST | /api/events | Creates a new event | Organiser | `{ eventName, description, eventDate, location }` | 201 Created with new event id; 400 Bad Request on invalid data; 401 Unauthorized if not logged in |
| PUT | /api/events/{id} | Updates an existing event | Organiser (owner only) | `{ eventName, description, eventDate, location }` | 200 OK with updated event; 403 Forbidden if not owning organiser; 404 Not Found if id doesn't exist |
| DELETE | /api/events/{id} | Deletes/cancels an event | Organiser (owner only) | (none) | 204 No Content; 403 Forbidden if not owner; 404 Not Found if id doesn't exist |

---

## 6. Categories (`/api/categories`)

| Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/events/{eventId}/categories | Lists all categories for a given event | None | (none) | 200 OK with array of categories; 404 Not Found if event doesn't exist |
| GET | /api/categories/{id} | Retrieves a single category's detail | None | (none) | 200 OK with category object; 404 Not Found if id doesn't exist |
| POST | /api/events/{eventId}/categories | Adds a category to an event | Organiser (owner of event) | `{ categoryName, distanceKm, maxParticipants, entryFee }` | 201 Created with new category id; 400 Bad Request on invalid data; 403 Forbidden if not owner |
| PUT | /api/categories/{id} | Updates a category | Organiser (owner of parent event) | `{ categoryName, distanceKm, maxParticipants, entryFee }` | 200 OK with updated category; 403 Forbidden if not owner; 404 Not Found if id doesn't exist |
| DELETE | /api/categories/{id} | Removes a category | Organiser (owner of parent event) | (none) | 204 No Content; 403 Forbidden if not owner; 409 Conflict if enrolments already exist |

---

## 7. Event Enrolments (`/api/enrolments`)

| Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/enrolments/mine | Lists the logged-in participant's own enrolments | Participant | (none) | 200 OK with array of enrolments |
| GET | /api/categories/{categoryId}/enrolments | Lists all participants enrolled in a category | Organiser (owner of parent event) | (none) | 200 OK with array of enrolments; 403 Forbidden if not owner |
| POST | /api/categories/{categoryId}/enrolments | Enrols the logged-in participant into a category | Participant | `{ }` (participant id from token) | 201 Created with enrolment id; 409 Conflict if already enrolled or category full; 401 Unauthorized if not logged in |
| DELETE | /api/enrolments/{id} | Cancels/withdraws an enrolment | Participant (own enrolment only) | (none) | 204 No Content; 403 Forbidden if not own enrolment; 404 Not Found if id doesn't exist |

---

## 8. Results (`/api/results`)

| Method | Route | Description | Role Required | Request Body | Expected Response |
|---|---|---|---|---|---|
| GET | /api/categories/{categoryId}/results | Lists all results for a category, ranked by position | None | (none) | 200 OK with array of results; 404 Not Found if category doesn't exist |
| GET | /api/results/{id} | Retrieves a single result | None | (none) | 200 OK with result object; 404 Not Found if id doesn't exist |
| POST | /api/enrolments/{enrolmentId}/results | Captures a result for an enrolment | Organiser (owner of parent event) | `{ finishTime, position }` | 201 Created with new result id; 400 Bad Request on invalid data; 403 Forbidden if not owner; 409 Conflict if a result already exists |
| PUT | /api/results/{id} | Corrects an existing result | Organiser (owner of parent event) | `{ finishTime, position }` | 200 OK with updated result; 403 Forbidden if not owner; 404 Not Found if id doesn't exist |
| DELETE | /api/results/{id} | Removes a result | Organiser (owner of parent event) | (none) | 204 No Content; 403 Forbidden if not owner; 404 Not Found if id doesn't exist |

---

## 9. Entity Relationship Summary

The full diagram is provided in `docs/ERD.pdf`. It covers six entities —
Roles, Users, Events, Categories, Enrolments and Results — with all attributes,
primary keys, foreign keys and cardinalities:

- Roles (1) — (Many) Users, via `RoleId`
- Users/Organiser (1) — (Many) Events, via `OrganiserId`
- Events (1) — (Many) Categories, via `EventId`
- Users/Participant (Many) — (Many) Categories, via the `Enrolments` junction table
- Enrolments (1) — (1) Results, via `EnrolmentId`
